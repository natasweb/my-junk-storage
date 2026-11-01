# Hooks

> 元URL: https://kiro.dev/docs/hooks/  
> 最終取り込み日: 2026-11-01  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

フックは、セッション内で特定のイベント（エージェントによるファイルの変更、ツールの起動、タスクの完了など）が発生した際に、シェルコマンドやエージェントのプロンプトを自動的に実行します。トリガーとアクションを定義すれば、Kiroが実行を処理します。

| 機能 | IDE | CLI | Web | モバイル |
| --- | --- | --- | --- | --- |
| イベント駆動型フック | ✓ | ✓ | — | — |
| シェルコマンドアクション | ✓ | ✓ | — | — |
| エージェントのプロンプトアクション | ✓ | ✓ | — | — |
| チャットでエージェントに尋ねてフックを作成する | ✓ | ✓ | — | — |

## フックでできること

- **標準の遵守** - エージェントがファイルを変更した後、リンター、フォーマッター、または型チェックを自動的に実行する
- **危険な操作の制限** - 前提条件が満たされない限り、ツールの実行をブロックする（PreToolUse）
- **関連ファイルの生成** — 新しいソースファイルが追加された際に、テスト、ドキュメント、翻訳を自動生成
- **コミット前の検証** — エージェントが変更を確定する前にコードの品質をチェック
- **コンテキストの注入** - エージェントの処理内容に基づいて、追加の指示をエージェントに渡す

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

このファイルは`.kiro/hooks/lint-on-save.json`に配置されており、手動での操作を必要とせず自動的に起動します。フックは、STDINを介して保存されたファイルのパスとセッションコンテキストを受け取ります。コマンドがイベントデータをどのように受け取るかについての詳細は、[「フックアクション」](https://kiro.dev/docs/hooks/actions/)を参照してください。

## フックの仕組み

プロジェクトフックの設定は、`.kiro/hooks/`に保存されたJSONファイルです。Kiro CLI V3は`~/.kiro/hooks/`からグローバルフックも読み込むため、1つのフックをすべてのワークスペースで実行できます。グローバルフックとワークスペースフックは並行して実行されます。各ファイルは、トリガーイベント、オプションのマッチャーパターン、およびアクションを持つ1つ以上のフックを定義します。

トリガーイベントが発生すると、Kiroはマッチャーをチェックします。一致した場合（またはマッチャーが指定されていない場合）、アクションが実行されます：

- **コマンドアクションは、**プロジェクトのルートディレクトリでシェルコマンドを実行します。このコマンドは、STDIN 経由でセッションコンテキストを JSON 形式で受け取ります。
- **エージェントアクションは**、現在の会話にプロンプトを挿入し、エージェントの挙動を制御します。

### トリガーの選択

タスクのトリガーを選択するには、[フックタイプ](https://kiro.dev/docs/hooks/types/)を使用します。これは公式のトリガー一覧であり、利用可能な機能やトリガー固有の動作が記載されています。

## フックファイルのスキーマ

各フックファイルは、`.kiro/hooks/<id>.json`にある独立したJSONファイルです。完全なスキーマは以下の通りです：

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
| `hooks[].name` | はい | フック用の、人間が読み取れる識別子 |
| `hooks[].description` | いいえ | ドキュメント用のみ |
| `hooks[].trigger` | はい | フックを起動するイベント（PascalCase - 「[フックの種類」](https://kiro.dev/docs/hooks/types/)を参照） |
| `hooks[].matcher` | いいえ | このフックをトリガーするイベントをフィルタリングします。`PreToolUse`/`PostToolUse`の場合、ツールセレクタ（ツールID、カテゴリタグ、`@mcp`、`@<server>`、またはワイルドカード）またはツールIDに対する正規表現を使用します（[フックの種類](https://kiro.dev/docs/hooks/types/#pre-tool-use)を参照）。ファイルイベントの場合、ファイルパスに対する正規表現を使用します。デフォルトでは常に一致します。 |
| `hooks[].action.type` | はい | `"command"`（シェルコマンド）または `"agent"`（プロンプトの挿入） |
| `hooks[].action.command` | 条件 | 実行するシェルコマンド（`type` が `"command"` の場合に必須） |
| `hooks[].action.prompt` | 条件 | 挿入するプロンプトテキスト（`type` が `"agent"` の場合に必須） |
| `hooks[].timeout` | いいえ | コマンドアクションのタイムアウト（秒単位）（デフォルト：60）。`0` を指定すると、タイムアウトは無効になります。エージェントアクションでは無視されます。 |
| `hooks[].enabled` | いいえ | `false` を設定すると、フックを削除せずにスキップします（デフォルト：`true`）。 |
| `hooks[].confirm` | いいえ | `Stop`コマンドフックが実行される前に確認を求める。「[確認プロンプト」](https://kiro.dev/docs/hooks/#confirmation-prompts)を参照。 |

### 確認プロンプト

`Stop`トリガーに設定されたコマンドフックは、実行前に確認を求めることができます。確認する質問と提示する選択肢を指定した`confirm`ブロックを追加します:

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

各選択肢には、`id`、ボタンに表示される`label`、およびその選択肢が選択された際にフックのコマンドを実行するかどうかを制御する`run`フラグがあります。

#### `confirmCommand` を使用した動的な確認オプション

実行時に質問を行うかどうか、また何を尋ねるかを決定するには、`confirm`ブロックにオプションの`confirmCommand`を追加します。コマンドはプロンプトが表示される前に実行され、その標準出力（stdout）がJSON形式でプロンプトを制御します：

- `{ "skip": true }` はプロンプトを表示せず、このターンにおけるフックの実行をスキップします
- `{ "question": "...", "options": [...] }`は、静的な質問やオプションを置き換えます

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

`confirmCommand`が0以外の終了コードで終了した場合、タイムアウトした場合、または無効なJSONを出力した場合は、静的な`question`および`options`がフォールバックとして使用されます。これにより、特定の条件下でのみ表示されるべきプロンプトに有用です。例えば、マーカーファイルを書き込んで、以降のターンで`{ "skip": true }`を返す「このセッション中は再度尋ねない」オプションなどが挙げられます。

### ファイル名と保存場所

- **ワークスペース内の場所**：プロジェクトのルートディレクトリにある`.kiro/hooks/`
- **グローバルな場所（CLI V3）**：すべてのワークスペースで実行されるフック用の `~/.kiro/hooks/`
- **命名**：`.json` というファイル名であれば何でも使用可能です。説明的なケバブケースの命名法を使用してください（例：`lint-on-save.json`、`guard-writes.json`）
- **1ファイルあたりのフックの数**：1つのファイル内で、`hooks`配列を使用して複数のフックを定義できます
- **起動**：セッションの開始時にフックが自動的に起動します。手動での登録は不要です

## フックの設定

Kiro パネルの「Agent Hooks」セクションにある「**+**」ボタンをクリックし、「**Ask Kiro」を選択してフックを作成します**。希望する内容を自然言語で記述します（例：「ファイルの保存後にテストを実行する」）。Kiro が会話を通じてフックの設定を生成します。

生成されたフックは、`.kiro/hooks/` に JSON ファイルとして保存されます。

[](/videos/hooks-ide.mp4?h=fe4115fb)

## 以前のバージョン

`.kiro/hooks/*.json`形式は、**IDE 1.0**および**CLI V3**で導入されました。以前のバージョンからアップグレードする場合：

- **IDE 0.x** **からのアップグレードの場合** — フックは従来の形式から、トリガー名が PascalCase 形式の独立した JSON ファイルへと変更されました。トリガーのマッピングについては[、「IDE 1.0 の新機能：フック」を](https://kiro.dev/docs/ide/whats-new-v1/hooks/)参照してください。
- **CLI 2.x** **からのアップグレードの場合** — `/upgrade-agent` を実行して、オブジェクト形式のフックをプロファイルスコープの配列に変換してください。プロファイルに依存せずに実行されるべき定義や、複数のサーフェスにまたがって実行されるべき定義は、独立した `.kiro/hooks/*.json` ファイルに移動してください。現在の配置とトリガーのマッピングについては、「[CLI V3 フックの移行」](https://kiro.dev/docs/cli/v3/hooks-migration/)を参照してください。

## 次の手順

- **[フックトリガー](https://kiro.dev/docs/hooks/types/)** - トリガータイプとそのユースケース
- **[フックアクション](https://kiro.dev/docs/hooks/actions/)** - コマンドおよびエージェントアクションの詳細
- **[管理](https://kiro.dev/docs/hooks/management/)** - フックの整理、編集、および保守
- **[ベストプラクティス](https://kiro.dev/docs/hooks/best-practices/)** - 効果的なフック設計のためのパターン
- **[例](https://kiro.dev/docs/hooks/examples/)** - 活用できるテンプレート
- **[トラブルシューティング](https://kiro.dev/docs/hooks/troubleshooting/)** - よくある問題と解決策


---

[← 前へ: Steering](../steering.md) | [↑ 親ページ](../../../../index.md) | [次へ →: Hook triggers](types.md)
