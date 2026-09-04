# Built-in tools

> 元URL: https://kiro.dev/docs/tools/  
> 最終取り込み日: 2026-09-05  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

Kiroのエージェントには、開発タスク用の組み込みツールが含まれています。これらは統合エージェントハーネスによって提供され、各インターフェース間で共有されます。デフォルトのエージェントはすべての組み込みツールにアクセスできますが、カスタムエージェントではこのアクセス権を制限することができます。

**概要：**

|  |  |
| --- | --- |
| **クロスサーフェス** | ファイルの読み書き、シェル、Web検索・取得、サブエージェント、コンテキスト、タスク追跡、イントロスペクション |
| **IDEおよびCLI** | フックの作成 |
| **IDEとWeb** | Powers (`kiro_powers`) |
| **IDEのみ** | バックグラウンドプロセス（開発用サーバー）、エディタのコード解析（`read_code`、セマンティックリネーム） |
| **CLIのみ** | コードインテリジェンス（`code`）、ゴール、セッション設定、MCP ツール検索（`tool_search`）、ナレッジ（実験的）、シンキング（実験的） |
| **非推奨** | `aws`、`delegate` - 置き換え予定 |
| **設定** | [カスタムエージェント](https://kiro.dev/docs/custom-agents/)を使用`tools`フィールド + [権限](https://kiro.dev/docs/permissions/) |

## ツールカタログ

以下のすべてのツールは、IDE、CLI、Web、およびモバイルで利用可能です。

| ツール | カテゴリ | 説明 |
| --- | --- | --- |
| `read_file` / `read_files` | ファイル | 1つまたは複数のファイルを読み込む |
| `list_directory` | ファイル | ディレクトリの内容を表示する |
| `file_search` | ファイル | ファイルパスのあいまい検索 (glob) |
| `grep_search` | ファイル | 正規表現によるコンテンツ検索 |
| `fs_write` | ファイル | ファイルの作成または上書き |
| `fs_append` | ファイル | 既存のファイルに追加する |
| `str_replace` | ファイル | 指定したテキストの置換 |
| `delete_file` | ファイル | ファイルを削除 |
| `execute_bash` | シェル | シェルコマンドの実行 |
| `web_search` | Web | Web検索 |
| `web_fetch` | Web | URLからコンテンツを取得する |
| `invoke_subagent` | エージェント | タスクを並行して委任する |
| `disclose_context` | コンテキスト | スキルの起動またはステアリング |
| `introspect` | コンテキスト | 公式ドキュメントに基づき、Kiro自身の機能に関する質問に回答する |
| `todo_list` | セッション | セッション内のタスクを追跡する |

### 利用可能なデバイス

一部のツールは、まだすべてのデバイスで利用できないものがあります：

| ツール | IDE | CLI | Web | モバイル | ステータス |
| --- | --- | --- | --- | --- | --- |
| `control_bash_process` | ✓ | — | — | — | GA |
| `get_process_output` | ✓ | — | — | — | GA |
| `list_processes` | ✓ | — | — | — | GA |
| `kiro_powers` | ✓ | — | ✓ | — | GA |
| `create_hook` | ✓ | ✓ | — | — | GA |
| `code` | — | ✓ | — | — | GA |
| `goal` | — | ✓ | — | — | GA |
| `session_settings` | — | ✓ | — | — | GA |
| `tool_search` | — | ✓ | — | — | GA |
| `knowledge` | — | ✓ | — | — | 実験的 |
| `thinking` | — | ✓ | — | — | 実験的 |
| `aws` | — | ✓ | — | — | 非推奨 |
| `delegate` | — | ✓ | — | — | 非推奨 → サブエージェントを使用してください |

バックグラウンドプロセスツール（`control_bash_process`、`get_process_output`、`list_processes`）は、IDE の[開発サーバー](https://kiro.dev/docs/ide/chat/dev-servers/)ワークフローを支えています。 `code`ツール（tree-sitterおよびLSPベースの[コードインテリジェンス](https://kiro.dev/docs/tools/code-intelligence/)）は、IDE以外のクライアント向けに提供されています。IDE内では、同等の機能がエディタネイティブツールとして組み込まれています。AST解析およびセマンティックリネームを行う`read_code`は、エディタがすでに実行している言語サーバーを利用しています。

## ツールへのアクセス設定

利用可能なツールカテゴリを制御するには、[カスタム](https://kiro.dev/docs/custom-agents/)エージェントの「`tools`」フィールドを使用します：

json

```json
{
  "tools": ["read", "write", "shell", "web"]
}
```

各カテゴリは一連のツールに対応しています：

| カテゴリ | 含まれるもの |
| --- | --- |
| `read` | read_file、read_files、list_directory、file_search、grep_search、code |
| `write` | fs_write、fs_append、str_replace、delete_file |
| `shell` | execute_bash、control_bash_process、get_process_output、list_processes |
| `web` | web_fetch、web_search |
| `subagent` | invoke_subagent |
| `spec` | Spec ワークフローツール |
| `context` | disclose_context、introspect、knowledge |

特別な値：

| 値 | 効果 |
| --- | --- |
| `@builtin` | すべての組み込みツール |
| `@mcp` | すべてのMCPサーバーツール |
| `*` | すべて（組み込み＋MCP） |

特定のカテゴリを除外：`"excludedTools": ["web"]`

[権限設定](https://kiro.dev/docs/permissions/)により、ツールの機能（単に表示されるかどうかだけでなく）を制御します。

## ファイルツール（読み取り）

| ツール | 説明 |
| --- | --- |
| `read_file` | 単一のファイル（テキストまたは画像）を読み込みます。エイリアス：`fs_read` |
| `read_files` | 行範囲を指定して複数のファイルを読み込む（オプション） |
| `list_directory` | ディレクトリの内容を詳細形式で一覧表示 |
| `file_search` | ファイルパスのあいまい検索（globパターン）。別名：`glob` |
| `grep_search` | ファイル全体での正規表現によるコンテンツ検索。別名：`grep` |
| `code` | AST ベースのコード解析 - シグネチャ、シンボル、ファジー検索 |

すべての読み取りツールは、`fs_read` [の権限ルール](https://kiro.dev/docs/permissions/)と機能ルールを適用します。`.kiroignore`のサポートは[環境によって異なります](https://kiro.dev/docs/kiroignore/)。CLI V3では、`file_search`および`grep_search`が、結果から無視されるパスをフィルタリングします。

## ファイルツール（書き込み）

| ツール | 説明 |
| --- | --- |
| `fs_write` | ファイルを作成または上書きします。別名：`write` |
| `fs_append` | 既存のファイルの末尾にコンテンツを追加します |
| `str_replace` | ファイル内の特定のテキストを置換する（対象を絞った編集） |
| `delete_file` | ファイルを削除する |

書き込みツールは、`fs_write` [権限](https://kiro.dev/docs/permissions/)ルールを順守します。

## シェルツール

| ツール | 説明 | サーフェス |
| --- | --- | --- |
| `execute_bash` | シェルコマンドを実行します。エイリアス: `shell`、`execute_cmd` | IDE · CLI · Web |
| `control_bash_process` | 長時間実行されるバックグラウンドプロセスの開始/停止 | IDE · CLI |
| `get_process_output` | バックグラウンドプロセスからの出力を読み取る | IDE · CLI |
| `list_processes` | 実行中のバックグラウンドプロセスの一覧表示 | IDE · CLI |

シェルツールは、`shell`の[権限](https://kiro.dev/docs/permissions/)ルールに従います。`match`のパターンを使用して、一般的なコマンドを事前に承認します:

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
| `web_search` | ウェブ上で最新情報を検索する |
| `web_fetch` | URLからテキストコンテンツを取得・抽出する |

**制限事項：**1回の取得につき最大10 MB、タイムアウト30秒、テキスト/HTMLのみ、再試行3回。

エンタープライズ管理者は、「**設定」>「共有設定」**から Web ツールを無効にできます。[「エンタープライズガバナンス - Web ツール」](https://kiro.dev/docs/enterprise/governance/web-tools/)を参照してください。

## コードインテリジェンス

[詳細ガイド →](https://kiro.dev/docs/tools/code-intelligence/)

18言語に対応したTree-sitterベースのコード理解機能 — シンボル検索、ドキュメントシンボル、パターン検索、ASTベースの書き換え。参照の検索、定義への移動、名前変更、診断機能のためのオプションのLSP統合。

**対応言語：**Bash、C、C++、C#、Elixir、Go、Java、JavaScript、Kotlin、Lua、PHP、Python、Ruby、Rust、Scala、Swift、TSX、TypeScript

## サブエージェントへの委任

| ツール | 説明 |
| --- | --- |
| `invoke_subagent` | 隔離されたコンテキストで実行されているエージェントにタスクを委譲する |

詳細なガイドについては、「[サブエージェントとして呼び出す」](https://kiro.dev/docs/custom-agents/subagents/)を参照してください。

## コンテキストツール

| ツール | 説明 |
| --- | --- |
| `disclose_context` | スキルやステアリングファイルをコンテキスト内で有効化する |
| `introspect` | インデックス化されたKiroドキュメントを検索（セマンティック検索＋BM25） |
| `knowledge` | インデックス化されたナレッジベースを検索（実験的） |

## ツール検索（CLI）

[詳細ガイド →](https://kiro.dev/docs/mcp/tool-search/)

すべてのリクエストにすべてのツール定義を含めるのではなく、オンデマンドでMCPツールを読み込みます。多数のMCPサーバー環境でもコンテキストを効率的に維持します。CLIで利用可能です。

**仕組み：**インデックス → 遅延コンパクトリスト → オンデマンドの`tool_search`呼び出し → 一致したツールの起動。

| パラメータ | タイプ | 説明 |
| --- | --- | --- |
| `tool_id` | 文字列 | `server_name::tool_name`の正確な識別子 |
| `query` | 文字列 | BM25検索のキーワード |
| `max_results` | 整数 | 最大結果数（デフォルト：5） |

CLI で有効にする: `kiro-cli settings toolSearch.enabled true`

| 設定 | デフォルト | 説明 |
| --- | --- | --- |
| `toolSearch.enabled` | `false` | マスターの切り替え |
| `toolSearch.minPct` | `5` | ツールの仕様がコンテキストのこの％を超えた場合に有効にする |
| `toolSearch.minTokens` | `50000` | ツールの仕様がこのトークン数を上回ったときに有効にする |

## セッションツール

| ツール | 説明 | ステータス |
| --- | --- | --- |
| `todo_list` | セッション内のタスクを追跡する（作成、完了、一覧表示） | GA |
| `create_hook` | チャットからエージェントフックを作成する | GA |
| `kiro_powers` | インストール済みのパワーの一覧表示、有効化、および使用 | GA |

## 次の手順

- [Webツール - 詳細ガイド](https://kiro.dev/docs/tools/web/)
- [コードインテリジェンス - 詳細ガイド](https://kiro.dev/docs/tools/code-intelligence/)
- [カスタムエージェント](https://kiro.dev/docs/custom-agents/) - エージェントがアクセスできるツールを設定する
- [権限](https://kiro.dev/docs/permissions/) - 各ツールの動作を制御する
- [MCP](https://kiro.dev/docs/mcp/) - 外部ツールによる拡張
- [構成スコープ](https://kiro.dev/docs/configuration/) - ツールの設定が保存される場所


---

[← 前へ: Checkpoints and rewind](../checkpoints.md) | [↑ 親ページ](../../../../index.md) | [次へ →: Web tools](web.md)
