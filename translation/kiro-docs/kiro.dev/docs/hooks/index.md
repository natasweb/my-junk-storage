# Hooks

> 元URL: https://kiro.dev/docs/hooks/  
> 最終取り込み日: 2026-10-06  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

フックは、セッション内で特定のイベント（エージェントによるファイルの変更、ツールの起動、タスクの完了など）が発生した際に、シェルコマンドやエージェントのプロンプトを自動的に実行します。トリガーとアクションを定義すれば、Kiroが実行を処理します。

| 機能 | IDE | CLI | Web | モバイル |
| --- | --- | --- | --- | --- |
| イベント駆動型フック | ✓ | ✓ | ✓ | — |
| シェルコマンドアクション | ✓ | ✓ | ✓ | — |
| エージェントのプロンプトアクション | ✓ | ✓ | ✓ | — |
| チャットでエージェントに尋ねてフックを作成する | ✓ | ✓ | ✓ | — |

## フックでできること

- **標準の遵守** - エージェントがファイルを変更した後、リンター、フォーマッター、または型チェックを自動的に実行する
- **危険な操作の制限** - 前提条件が満たされない限り、ツールの実行をブロックする（PreToolUse）
- **関連ファイルの自動生成** — 新しいソースファイルが追加された際に、テスト、ドキュメント、翻訳を自動生成
- **コミット前の検証** — エージェントが変更を確定する前にコードの品質をチェック
- **コンテキストの注入** - エージェントの動作内容に基づいて、追加の指示をエージェントに与える

## 簡単な例

エージェントがTypeScriptファイルを保存または編集するたびにESLintを実行する`PostFileSave`フック：

json

```json
{
  "version": "v1",
  "hooks": [{
    "name": "Lint on save",
    "trigger": "PostFileSave",
    "matcher": "\\.(ts|tsx)$",
    "action": { "type": "command", "command": "npx eslint --fix" }
  }]
}
```

このファイルは `.kiro/hooks/lint-on-save.json` に配置されており、手動での操作を必要とせず自動的に起動します。このフックは、STDIN 経由で保存されたファイルのパスとセッションコンテキストを受け取ります。コマンドがイベントデータをどのように受け取るかについての詳細は、「[フックアクション」](https://kiro.dev/docs/hooks/actions/)を参照してください。

## フックの仕組み

フックの設定は、`.kiro/hooks/` に保存された JSON ファイルです。各ファイルは、トリガーイベント、オプションのマッチャーパターン、およびアクションを持つ 1 つ以上のフックを定義します。

トリガーイベントが発生すると、Kiroはマッチャーをチェックします。一致した場合（またはマッチャーが指定されていない場合）、アクションが実行されます：

- **コマンドアクションは、**プロジェクトのルートディレクトリでシェルコマンドを実行します。コマンドは、STDIN 経由でセッションコンテキストを JSON 形式で受け取ります。
- **エージェントアクションは**、現在の会話にプロンプトを挿入し、エージェントの挙動を制御します。

### トリガーの選択

[「フックタイプ」](https://kiro.dev/docs/hooks/types/)を使用して、タスクのトリガーを選択します。これは公式のトリガー一覧であり、利用可能な機能やトリガー固有の動作が記載されています。

## フックファイルのスキーマ

各フックファイルは、`.kiro/hooks/<id>.json` にある独立した JSON ファイルです。完全なスキーマは以下の通りです：

json

```json
{
  "version": "v1",
  "hooks": [
    {
      "name": "example-hook",
      "trigger": "PostFileSave",
      "matcher": "\\.(ts|tsx)$",
      "action": { "type": "command", "command": "npx eslint --fix" }
    }
  ]
}
```

### フィールドリファレンス

| フィールド | 必須 | 説明 |
| --- | --- | --- |
| `version` | はい | スキーマのバージョン - 現在 `"v1"` |
| `hooks` | はい | フック定義の配列 |
| `hooks[].name` | はい | フック用の人間が読みやすい識別子 |
| `hooks[].description` | いいえ | ドキュメント用のみ |
| `hooks[].trigger` | はい | フックを起動するイベント（PascalCase - 「[フックの種類」](https://kiro.dev/docs/hooks/types/)を参照） |
| `hooks[].matcher` | いいえ | このフックを起動するイベントをフィルタリングするための正規表現パターン。`PreToolUse`/`PostToolUse` の場合、ツール名と一致します。ファイルイベントの場合、ファイルパスと一致します。デフォルトでは常に一致します。 |
| `hooks[].action.type` | はい | `"command"` （シェルコマンド）または `"agent"`（プロンプトの挿入） |
| `hooks[].action.command` | 条件 | 実行するシェルコマンド（`type`が`"command"`の場合に必須） |
| `hooks[].action.prompt` | 条件 | 挿入するプロンプトテキスト（`type` が `"agent"` の場合に必須） |
| `hooks[].timeout` | なし | コマンドアクションのタイムアウト（秒単位）（デフォルト：60）。`0` を指定すると、タイムアウトは無効になります。エージェントアクションでは無視されます。 |
| `hooks[].enabled` | いいえ | `false` を設定して、フックを削除せずにスキップします（デフォルト：`true`） |
| `hooks[].confirm` | いいえ | `Stop` コマンドフックが実行される前に確認を求める。「[確認プロンプト」](https://kiro.dev/docs/hooks/#confirmation-prompts)を参照してください。 |

### 確認プロンプト

`Stop`トリガー上のコマンドフックは、実行前に確認を求めることができます。確認する質問と提示する選択肢を指定して、`confirm`ブロックを追加します:

json

```json
{
  "version": "v1",
  "hooks": [
    {
      "name": "Submit session results",
      "trigger": "Stop",
      "action": { "type": "command", "command": "./submit.sh" },
      "confirm": {
        "question": "Submit this session's results?",
        "options": [
          { "id": "submit", "label": "Yes, submit", "run": true },
          { "id": "dismiss", "label": "Not this time", "run": false }
        ]
      }
    }
  ]
}
```

各オプションには、`id`、ボタンに表示される`label`、およびそのオプションが選択された際にフックのコマンドを実行するかどうかを制御する`run`フラグがあります。

#### `confirmCommand` を使用した動的な確認オプション

実行時に確認を行うかどうか、および何を尋ねるかを決定するには、`confirm` ブロックにオプションの `confirmCommand` を追加します。このコマンドはプロンプトが表示される前に実行され、その標準出力（stdout）が JSON 形式でプロンプトを制御します：

- `{ "skip": true }` プロンプトを表示せず、このターンにおけるフックをスキップします
- `{ "question": "...", "options": [...] }` 静的な質問とオプションを置き換えます

json

```json
{
  "confirm": {
    "question": "Submit this session's results?",
    "confirmCommand": "./confirm-options.sh",
    "options": [
      { "id": "submit", "label": "Yes, submit", "run": true },
      { "id": "dismiss", "label": "Not this time", "run": false }
    ]
  }
}
```

`confirmCommand`が0以外の値で終了した場合、タイムアウトした場合、または無効なJSONを出力した場合は、フォールバックとして静的な`question`および`options`が使用されます。これにより、特定の条件下でのみ表示されるべきプロンプトに役立ちます。例えば、マーカーファイルを書き込み、以降のターンで`{ "skip": true }`を返す「このセッション中は再度尋ねない」オプションなどが挙げられます。

### ファイル名と保存場所

- **保存場所**：プロジェクトのルートディレクトリ内の `.kiro/hooks/`
- **命名**：`.json` というファイル名であれば何でも構いません。説明的なケバブケースの命名法を使用してください（例：`lint-on-save.json`、`guard-writes.json`）
- **1ファイルあたりのフックの数**：1つのファイル内で、`hooks`配列に複数のフックを定義できます
- **有効化**：セッションの開始時にフックは自動的に有効化されます。手動での登録は不要です

## フックの設定

Kiroパネルの「Agent Hooks」セクションにある「**+**」ボタンをクリックし、「**Ask Kiro」を選択してフックを作成します**。希望する内容を自然な言葉で記述します（例：「ファイル保存のたびにテストを実行する」）。Kiroが会話を通じてフックの設定を生成します。

生成されたフックは、`.kiro/hooks/` 内の JSON ファイルとして保存されます。

[](/videos/hooks-ide.mp4?h=fe4115fb)

## 以前のバージョン

`.kiro/hooks/*.json` 形式は、**IDE 1.0** および **CLI 3.0** で導入されました。以前のバージョンからアップグレードする場合は、以下の点に注意してください：

- **IDE 0.x** **からのアップグレードの場合** — フックは従来の形式から、トリガー名が PascalCase 形式の独立した JSON ファイルへと変更されました。トリガーのマッピングについては[、「IDE 1.0 の新機能: フック」](https://kiro.dev/docs/ide/whats-new-v1/hooks/)を参照してください。
- **CLI 2.x** **からのアップグレードの場合** — フックは、エージェント設定内の埋め込みフィールドから独立したファイルへ移行されました。`kiro-cli agent migrate` を実行して自動変換を行うか、手動でのマッピングについては「[CLI 3.0 フックの移行」](https://kiro.dev/docs/cli/v3/hooks-migration/)を参照してください。従来の埋め込み形式およびトリガーネームスペースについては、[CLI 2.x リファレンス](https://kiro.dev/docs/cli/2x-reference/#hooks)を参照してください。

## 次の手順

- **[フックトリガー](https://kiro.dev/docs/hooks/types/)** - トリガータイプとそのユースケース
- **[フックアクション](https://kiro.dev/docs/hooks/actions/)** - コマンドおよびエージェントアクションの詳細
- **[管理](https://kiro.dev/docs/hooks/management/)** - フックの整理、編集、および保守
- **[ベストプラクティス](https://kiro.dev/docs/hooks/best-practices/)** - 効果的なフック設計のためのパターン
- **[例](https://kiro.dev/docs/hooks/examples/)** - 活用できるテンプレート
- **[トラブルシューティング](https://kiro.dev/docs/hooks/troubleshooting/)** - よくある問題と解決策


---

[← 前へ: Steering](../steering.md) | [↑ 親ページ](../../../../index.md) | [次へ →: Hook triggers](types.md)
