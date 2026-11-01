# Built-in tools

> 元URL: https://kiro.dev/docs/tools/  
> 最終取り込み日: 2026-11-01  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

Kiroのエージェントには、開発タスク用の組み込みツールが含まれています。これらは統合エージェント・ハーネスによって提供され、各インターフェース間で共有されます。デフォルトのエージェントはすべての組み込みツールにアクセスできますが、カスタムエージェントではこのアクセス権を制限することができます。

**概要：**

|  |  |
| --- | --- |
| **クロスサーフェス** | ファイルの読み書き、シェル、Web検索・取得、サブエージェント、コンテキスト、イントロスペクション |
| **IDEおよびCLI** | フックの作成およびオンデマンドでのMCPツールの読み込み |
| **IDE限定** | バックグラウンドプロセスの制御と出力、開発サーバーのオーケストレーション、エディタのコード分析（`read_code`、セマンティックリネーム） |
| **IDEおよびWeb** | Powers（`kiro_powers`） |
| **CLIのみ** | コードインテリジェンス（`code`）およびナレッジ（実験的機能） |
| **CLI 2.x レガシー** | `session`、`tool_search` (V2)、`thinking` (実験的)、`use_aws` (推奨エイリアス `aws`)、`delegate`、`task` (エイリアス `todo`、`todo_list`)、`goal`; V3 の組み込み関数ではない |
| **設定** | [カスタムエージェント](https://kiro.dev/docs/custom-agents/)を使用`tools`フィールド + [権限](https://kiro.dev/docs/permissions/) |

## ツールカタログ

以下のカタログは、Kiro の各サーフェスにまたがる組み込みツールを網羅しています。利用可能性は、サーフェスやエージェントエンジンによって異なる場合があります。

| ツール | カテゴリ | 説明 |
| --- | --- | --- |
| `read_file` | ファイル | 単一のファイルを読み込む |
| `list_directory` | ファイル | ディレクトリの内容を表示する |
| `file_search` | ファイル | ファイルパスのあいまい検索 (glob) |
| `grep_search` | ファイル | 正規表現によるコンテンツ検索 |
| `fs_write` | ファイル | ファイルの作成または上書き |
| `fs_append` | ファイル | 既存のファイルに追加する |
| `str_replace` | ファイル | 指定したテキストの置換 |
| `delete_file` | ファイル | ファイルを削除 |
| `execute_bash` | シェル | シェルコマンドの実行 |
| `remote_web_search` | Web | Webを検索（`web_search`として表示されます） |
| `web_fetch` | Web | URLからコンテンツを取得する |
| `invoke_sub_agent` | エージェント | タスクを並行して委任する |
| `disclose_context` | コンテキスト | スキルの起動またはステアリング |
| `introspect` | コンテキスト | 公式ドキュメントに基づき、Kiro自身の機能に関する質問に回答する |

### 利用可能なプラットフォーム

一部のツールは、まだすべてのプラットフォームやエージェントエンジンで利用可能ではありません。CLI V3にはタスクリストツールがないため、`chat.enableTodoList`の設定はそこで効果を発揮しません：

| ツール | IDE | CLI | Web | モバイル | ステータス |
| --- | --- | --- | --- | --- | --- |
| `control_bash_process` / `control_pwsh_process` | ✓ | — | — | — | GA |
| `get_process_output` | ✓ | — | — | — | GA |
| `list_processes` | ✓ | — | — | — | GA |
| `kiro_powers` | ✓ | — | ✓ | — | GA |
| `createHook` | ✓ | ✓ | — | — | GA |
| `code` | — | ✓ | — | — | GA |
| `session` | — | 2.x | — | — | CLI 2.x のみ |
| `tool_search` | — | V2 | — | — | CLI V2のみ |
| `knowledge` | — | ✓ | — | — | 実験的 |
| `thinking` | — | 2.x | — | — | CLI 2.x のみ |
| `use_aws` / `aws` | — | 2.x | — | — | CLI 2.x のみ → MCP サーバーを使用 |
| `delegate` | — | 2.x | — | — | CLI 2.x のみ → サブエージェントを使用 |
| `task`（エイリアス：`todo`、`todo_list`） | — | 2.x | — | — | CLI 2.x のみ |
| `goal` | — | 2.x | — | — | CLI 2.x のみ。設定した `/goal` の完了が確認されたことを報告します |

バックグラウンドプロセスツール（`control_bash_process`、`control_pwsh_process`、`get_process_output`、`list_processes`）は IDE で利用可能であり、[開発サーバー](https://kiro.dev/docs/ide/chat/dev-servers/)をサポートしています。CLI V3 では、`run_in_background` を使用して `execute_bash`（Windows では `execute_pwsh`）を通じてバックグラウンドでコマンドを開始できますが、これらのプロセス制御ツールは登録されません。 `code`ツール（tree-sitterおよびLSPベースの[コードインテリジェンス](https://kiro.dev/docs/tools/code-intelligence/)）は、IDE以外のクライアント向けに提供されています。IDE内では、同等の機能がエディタネイティブツールとして組み込まれており、AST解析やセマンティックリネームを行う`read_code`は、エディタが既に実行している言語サーバーを利用しています。

## ツールへのアクセス設定

[カスタム](https://kiro.dev/docs/custom-agents/)エージェントの`tools`フィールドを使用して、利用可能なカテゴリタグや直接ツールセレクタを制御します：

json

```json
{
  "tools": ["read", "write", "shell", "web"]
}
```

一般的なカテゴリのマッピングには、次のようなものがあります：

| カテゴリ | 通常、以下が含まれます |
| --- | --- |
| `read` | read_file、list_directory、file_search、grep_search、code、`tool_search`（V2）、introspect |
| `write` | fs_write、fs_append、str_replace、delete_file、code |
| `shell` | execute_bash または execute_pwsh。IDE クライアントは、control_bash_process または control_pwsh_process、get_process_output、list_processes も公開している場合があります。 |
| `web` | web_fetch、remote_web_search |
| `subagent` | エージェントごとのディスパッチツール（`subagent/<agent>` として表示されます） |

`knowledge` や `invoke_sub_agent` など、一部の組み込み機能にはカテゴリタグがありません。これらを選択するには、ツール ID を使用するか、`@builtin` または `*` を使用してください。`invoke_sub_agent` は任意のエージェントを起動できるため、`tools` に `@builtin` または `*` を含むエージェントは、任意のサブエージェントを起動できます。

特別な値：

| 値 | 効果 |
| --- | --- |
| `@builtin` | すべての組み込みツール |
| `@mcp` | すべてのMCPサーバーツール |
| `*` | すべて（組み込みツール＋MCP） |

特定のカテゴリを除外する：`"excludedTools": ["web"]`

[権限設定](https://kiro.dev/docs/permissions/)により、ツールの機能（単に表示されるかどうかだけでなく）を制御します。

## ファイルツール（読み取り）

| ツール | 説明 |
| --- | --- |
| `read_file` | 単一のファイル（テキストまたは画像）を読み取ります。複数のファイルを読み取るには、ファイルごとに 1 回ずつ呼び出してください。移行中、CLI 2.x のエイリアス `fs_read` は `read` タグにマッピングされます。これはフックやランタイム ID ではありません。 |
| `list_directory` | ディレクトリの内容を詳細形式で一覧表示 |
| `file_search` | ファイルパスのあいまい検索（globパターン）。CLI 2.xでの名称：`glob` |
| `grep_search` | ファイル全体での正規表現によるコンテンツ検索。CLI 2.x での名称：`grep` |
| `code` | AST ベースのコード分析 - シグネチャ、シンボル、ファジー検索 |

すべての読み取りツールは、`fs_read` [権](https://kiro.dev/docs/permissions/)限ルールが適用されます。`.kiroignore` のサポートは[表面によって異なります](https://kiro.dev/docs/kiroignore/)。CLI V3 では、一致する直接読み取りはブロックされますが、`file_search` および `grep_search` は、結果から無視されるパスをフィルタリングします。

## ファイルツール（書き込み）

| ツール | 説明 |
| --- | --- |
| `fs_write` | ファイルを作成または上書きします。CLI 2.x の名前：`write` |
| `fs_append` | 既存のファイルの末尾にコンテンツを追加します。 |
| `str_replace` | ファイル内の特定のテキストを置換する（対象を絞った編集） |
| `delete_file` | ファイルを削除する |

書き込みツールは、`fs_write` [権限](https://kiro.dev/docs/permissions/)ルールを順守します。

## シェルツール

| ツール | 説明 | サーフェス |
| --- | --- | --- |
| `execute_bash` | シェルコマンドを実行します。CLI 2.x の名前：`shell`、`execute_cmd` | IDE · CLI · Web |
| `control_bash_process` / `control_pwsh_process` | 長時間実行されるバックグラウンドプロセスの開始/停止 | IDE |
| `get_process_output` | バックグラウンドプロセスからの出力を読み取る | IDE |
| `list_processes` | 実行中のバックグラウンドプロセスの一覧表示 | IDE |

シェルツールは、`shell`[の権限ルール](https://kiro.dev/docs/permissions/)に従います。`match`パターンを使用して、一般的なコマンドを事前に承認します:

yaml

```yaml
rules:
  - capability: shell
    match: ["git *", "npm *", "cargo *"]
    effect: allow
  - capability: shell
    match: ["rm -rf *", "sudo *"]
    effect: deny
```

## Webツール

[詳細ガイド →](https://kiro.dev/docs/tools/web/)

| ツール | 説明 |
| --- | --- |
| `remote_web_search` | ウェブ上で最新情報を検索します。`web_search`として表示されます |
| `web_fetch` | URLからテキストコンテンツを取得・抽出します |

**制限事項：**1回の取得あたり最大10 MB、タイムアウトは30秒、テキスト/HTMLのみ。再試行の挙動はインターフェースによって異なります。CLI V3では、一時的な失敗に対して1回再試行を行います。

エンタープライズ管理者は、**[設定] > [共有設定]** から Web ツールを無効にできます。[「エンタープライズガバナンス - Web ツール」](https://kiro.dev/docs/enterprise/governance/web-tools/)を参照してください。

## コードインテリジェンス

[詳細ガイド →](https://kiro.dev/docs/tools/code-intelligence/)

18言語に対応したTree-sitterベースのコード理解機能 — シンボル検索、ドキュメントシンボル、パターン検索、およびASTベースの書き換え。参照の検索、定義への移動、名前変更、診断機能のためのオプションのLSP統合。

**対応言語：**Bash、C、C++、C#、Elixir、Go、Java、JavaScript、Kotlin、Lua、PHP、Python、Ruby、Rust、Scala、Swift、TSX、TypeScript

## サブエージェントへの委任

| ツール | 説明 |
| --- | --- |
| `invoke_sub_agent` | 隔離されたコンテキストで実行中のエージェントにタスクを委任する |

詳細なガイドについては、「[サブエージェントとして呼び出す」](https://kiro.dev/docs/custom-agents/subagents/)を参照してください。

## コンテキストツール

| ツール | 説明 |
| --- | --- |
| `disclose_context` | スキルやステアリングファイルをコンテキスト内で有効にする |
| `introspect` | インデックス化されたKiroドキュメントを検索（セマンティック検索＋BM25） |
| `knowledge` | インデックス化されたナレッジベースを検索（実験的） |

## ツール検索

[詳細ガイド →](https://kiro.dev/docs/mcp/tool-search/)

Kiro IDE 1.3 以降では、MCP ツールがオンデマンドで読み込まれます。Kiro CLI 2.31 以降のローカル V3 セッションでも同様です。Kiro はコンパクトなツール一覧を受け取り、必要な定義を読み込んだ後、通常の権限チェックおよびフックチェックを経て、それらのツールを呼び出します。

V3アクティビティでは、`tool_call`の前に`tool_load`が表示される場合があります。V2セッションでは、レガシーな`tool_search`パスを有効にできます。[ツール検索ガイド](https://kiro.dev/docs/mcp/tool-search/#enable-tool-search-in-v2-sessions)を参照してください。

## セッションツール

| ツール | 説明 | ステータス |
| --- | --- | --- |
| `createHook` | チャットからエージェントフックを作成する | GA |
| `kiro_powers` | インストール済みのパワーの一覧表示、有効化、および使用 | GA |

## 次の手順

- [Webツール - 詳細ガイド](https://kiro.dev/docs/tools/web/)
- [コードインテリジェンス - 詳細ガイド](https://kiro.dev/docs/tools/code-intelligence/)
- [カスタムエージェント](https://kiro.dev/docs/custom-agents/) - エージェントがアクセスできるツールを設定する
- [権限](https://kiro.dev/docs/permissions/) - 各ツールの動作を制御する
- [MCP](https://kiro.dev/docs/mcp/) - 外部ツールによる機能拡張
- [設定スコープ](https://kiro.dev/docs/configuration/) - ツールの設定が保存される場所


---

[← 前へ: Checkpoints and rewind](../checkpoints.md) | [↑ 親ページ](../../../../index.md) | [次へ →: Web tools](web.md)
