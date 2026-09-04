# Code intelligence

> 元URL: https://kiro.dev/docs/tools/code-intelligence/  
> 最終取り込み日: 2026-09-05  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

コードインテリジェンスは、コードを理解するための2つの相互に補完し合うレイヤーを提供します。

- **Tree-sitter（組み込み機能）** - 18の言語に対応した、導入直後から利用可能なコードインテリジェンス。追加のインストールなしで、シンボルの検索、ドキュメントのアウトライン表示、定義の参照が可能です。
- **LSP 統合（オプション）** - 参照の検索、定義への移動、ホバー時のドキュメント表示、リネームリファクタリング、診断機能により精度が向上します。言語サーバーのインストールが必要です。

以下のTree-Sitterの機能はすべて、IDE、CLI、Web、モバイルなど、あらゆるプラットフォームで利用可能です。LSPの機能は言語サーバーが必要であり、IDEおよびCLIでのみ利用可能です。

| 機能 | LSPが必要 |
| --- | --- |
| シンボル検索（ファジー検索） | — |
| ドキュメントシンボル | — |
| パターン検索（AST） | — |
| パターンの書き換え（AST） | — |
| コードベースの概要 | — |
| 参照を検索 | ✓ |
| 定義へ移動 | ✓ |
| シンボルの名前変更 | ✓ |
| 診断情報の取得 | ✓ |
| ドキュメントのホバー表示 | ✓ |

## 対応言語

Bash、C、C++、C#、Elixir、Go、Java、JavaScript、Kotlin、Lua、PHP、Python、Ruby、Rust、Scala、Swift、TSX、TypeScript

## シンボル検索

あいまい検索を使用して、名前から関数、クラス、メソッドを検索します:

```text
> Find the UserRepository class

Searching for symbols matching: "UserRepository" (using tool: code)
✓ Found 1 match

Class UserRepository at src/repositories/user.repository.ts:15:1
```

## パターン検索

AST ベースの構造的コード検索。テキストだけでなく、構造に基づいてコードを検索します。

### メタ変数

| パターン | 一致 |
| --- | --- |
| `$VAR` | 単一のノード（識別子、式） |
| `$$$` | 0個以上のノード（ステートメント、パラメータ） |

### 例

javascript

```javascript
// Find all console.log calls
pattern: console.log($ARG)
language: javascript

// Find all async functions
pattern: async function $NAME($$$PARAMS) { $$$ }
language: typescript

// Find all .unwrap() calls
pattern: $E.unwrap()
language: rust
```

## パターンの書き換え

ASTパターンを用いた自動コード変換:

javascript

```javascript
// Convert var to const
pattern: var $N = $V
replacement: const $N = $V
language: javascript

// Modernize hasOwnProperty
pattern: $O.hasOwnProperty($P)
replacement: Object.hasOwn($O, $P)
language: javascript

// Convert unwrap to expect
pattern: $E.unwrap()
replacement: $E.expect("unexpected None")
language: rust
```

## コードベースの概要

ワークスペースやディレクトリの概要を把握する:

エージェントはプロジェクトを調査する際、自動的にコードインテリジェンスを活用します。「このコードベースの概要を教えて」または「このプロジェクトは何を目的としているのか？」と尋ねてみてください

## ドキュメントの生成

コードベースの分析に基づいてプロジェクトのドキュメントを生成します:

エージェントに「このプロジェクトのREADMEを生成して」または「AGENTS.mdファイルを作成して」と尋ねてください。

## LSP 統合

言語サーバーがインストールされていると、コードインテリジェンスの精度がさらに向上します：

- **参照の検索** - シンボルのすべての使用箇所を特定
- **定義へ移動** — シンボルが定義されている場所へ移動
- **シンボルの名前変更** — コードベース全体で名前を変更
- **診断情報の取得** — ファイルのエラーおよび警告を表示
- **ホバーによるドキュメント表示** - カーソル位置の情報表示

IDE では、LSP による機能は、すでにインストール済みの言語拡張機能によって提供されます。CLI では、`/code init` を実行してワークスペースごとに有効にします。詳細は以下のセットアップリファレンスを参照してください。

## CLI での LSP の設定

[](/videos/code-intelligence.mp4?h=50de247e)

### 仕組み

Kiro CLIは、バックグラウンドでLSPサーバープロセスを起動し、stdioを介してJSON-RPCで通信を行います。ワークスペースを初期化すると、プロジェクトマーカー（`package.json`、`Cargo.toml`など）やファイル拡張子から言語を検出し、適切な言語サーバーを起動します。これらのサーバーはコードを継続的に分析し、シンボル、型、参照のインデックスを維持します。 クエリを実行すると、Kiroは自然言語をLSPプロトコルのリクエストに変換し、関連するサーバーに送信した後、応答を読みやすい出力形式に整えます。

### LSPの初期化

プロジェクトのルートディレクトリで、次のスラッシュコマンドを実行します：

```
/code init
```

これにより、`.kiro/settings/lsp.json` が作成され、言語サーバーが起動します：

```
✓ Workspace initialization started

Workspace: /path/to/your/project
Detected Languages: ["python", "rust", "typescript"]
Project Markers: ["Cargo.toml", "package.json"]

Available LSPs:
○ clangd (cpp) - available
○ gopls (go) - not installed
◐ jdtls (java) - initializing...
✓ pyright (python) - initialized (687ms)
✓ rust-analyzer (rust) - initialized (488ms)
○ solargraph (ruby) - not installed
✓ typescript-language-server (typescript) - initialized (214ms)
```

**ステータスインジケーター：**✓ 初期化済みで利用可能 · ◐ 初期化中 · ○ 利用可能（インストール済みだが不要） · ○ 未インストール

- **LSPサーバーの再起動：**言語サーバーが停止したり応答しなくなったりした場合は、`/code init -f` を実行してください。
- **自動初期化:** 最初の ``/code init`` 実行後、ワークスペースに ``.kiro/settings/lsp.json`` が存在する場合、Kiro CLI は起動時にコードインテリジェンスを自動的に初期化します。
- **無効化：**`.kiro/settings/lsp.json` を削除すると無効になります。`/code init` を実行することで、いつでも再有効化できます。

### サポートされている LSP サーバー

| 言語 | 拡張機能 | サーバー | インストールコマンド |
| --- | --- | --- | --- |
| TypeScript/JavaScript | `.ts`, `.js`, `.tsx`, `.jsx` | `typescript-language-server` | `npm install -g typescript-language-server typescript` |
| Rust | `.rs` | `rust-analyzer` | `rustup component add rust-analyzer` |
| Python | `.py` | `pyright` | `pip install pyright` |
| Go | `.go` | `gopls` | `go install golang.org/x/tools/gopls@latest` |
| Java | `.java` | `jdtls` | `brew install jdtls` (macOS) |
| Ruby | `.rb` | `solargraph` | `gem install solargraph` |
| C/C++ | `.c`, `.cpp`, `.h`, `.hpp` | `clangd` | `brew install llvm` (macOS) または `apt install clangd` (Linux) |
| Kotlin | `.kt`, `.kts` | `kotlin-language-server` | `brew install kotlin-language-server` |

### 言語サーバーの使用

自然言語でセマンティックなコードインテリジェンスをクエリ — シンボルの検索、定義への移動、参照の検索、ファイル横断での名前変更、診断情報の取得、ドキュメントの表示、利用可能なAPIの発見：

```
> Find references of Person class

Finding all references at: auth.ts:42:10
  1. src/auth.ts:42:10 - export function authenticate(...)
  2. src/handlers/login.ts:15:5 - authenticate(credentials)
  3. src/handlers/api.ts:89:12 - await authenticate(token)
```

```
> Dry run: rename the method "FetchUser" to "fetchUserData"

Dry run: Would rename 12 occurrences in 5 files
```

```
> What methods are available on the s3Client instance?

Available completions:
  1. putObject - Function: (params: PutObjectRequest) => Promise<PutObjectOutput>
  2. getObject - Function: (params: GetObjectRequest) => Promise<GetObjectOutput>
  3. deleteObject - Function: (params: DeleteObjectRequest) => Promise<DeleteObjectOutput>
```

### カスタム言語サーバー

`.kiro/settings/lsp.json` を編集して、カスタム言語サーバーを追加します：

json

```json
{
  "languages": {
    "mylang": {
      "name": "my-language-server",
      "command": "my-lsp-binary",
      "args": ["--stdio"],
      "file_extensions": ["mylang", "ml"],
      "file_patterns": ["Mylangfile", "mylang.config.*"],
      "project_patterns": ["mylang.config"],
      "exclude_patterns": ["**/build/**"],
      "multi_workspace": false,
      "initialization_options": { "custom": "options" },
      "request_timeout_secs": 60
    }
  }
}
```

**フィールド：**

- **name**: 言語サーバーの表示名
- **command**: 実行するバイナリまたはコマンド
- **args**: コマンドライン引数（通常は ``["--stdio"]``）
- **file_extensions**: このサーバーが処理するファイル拡張子
- **file_extensions**: ファイル名全体に対して照合されるグロブパターンのリスト（オプション）。`Dockerfile`、`Dockerfile.*`、`docker-compose*.yml` など、標準的な拡張子を持たない特定のファイル名を対象とする言語サーバーで使用します。宣言順序に関係なく、完全一致がグロブより優先され、より具体的なグロブがより広範なグロブより優先されます。
- **project_patterns**: プロジェクトのルートを示すファイル（例：`package.json`）
- **exclude_patterns**: 解析から除外するグロブパターン
- **multi_workspace**: LSPが複数のワークスペースフォルダをサポートしている場合はtrueに設定します（デフォルト: false）
- **initialization_options**: 初期化時に渡される LSP 固有の設定
- **request_timeout_secs**: LSP リクエストのタイムアウト時間（秒単位）。デフォルトは 60 です。

編集後は、Kiro CLI を再起動して新しい設定を読み込んでください。

### CLI コマンド

| コマンド | 目的 |
| --- | --- |
| `/code init` | 現在のディレクトリでコードインテリジェンスを初期化します |
| `/code init -f` | 再初期化を強制実行（すべての LSP サーバーを再起動） |
| `/code status` | ワークスペースのステータスと LSP サーバーの状態を表示する |
| `/code overview [path] [--silent]` | コードベース構造の概要 |
| `/code summary` | 対話型ドキュメント生成 |
| `/code logs` | トラブルシューティング用の LSP ログを表示 |

`/code logs` オプション：`-l, --level <LEVEL>` フィルタ (ERROR、WARN、INFO、DEBUG、TRACE、デフォルトは ERROR) · `-n, --lines <N>` 行数 (デフォルトは 20) · `-p, --path <PATH>` ログを JSON ファイルにエクスポート。

### トラブルシューティング

| 問題 | 原因 | 解決策 |
| --- | --- | --- |
| このエージェントでコードツールが有効になっていない | エージェントのツール一覧にコードツールが含まれていない | エージェントのツール配列に ``"code"`` を追加するか、`@builtin` を使用してすべての組み込みツールを含めるか、`@builtin/code` を使用してください |
| ワークスペースの初期化がまだ進行中です | LSPサーバーが起動中です | しばらく待ってから再試行してください。サーバーがクラッシュした場合は、`/code init -f` を使用して再起動してください |
| LSPの初期化に失敗しました |  | 詳細についてはログを確認してください：`/code logs -l ERROR` |
| シンボルが見つかりません | 言語サーバーがまだインデックス作成中であるか、ファイルに構文エラーがあるか、シンボル名が一致しない可能性があります | ファイルにエラーがないか確認し、検索条件を広げてみてください |
| 定義が見つかりません | その位置がシンボルを指していない | 行番号と列番号がシンボル名を指していることを確認してください |

すべての言語サーバーがすべての操作をサポートしているわけではありません（名前の変更や書式設定をサポートしていないものもあります）。また、大規模なコードベースの場合、最初のインデックス作成に時間がかかることがあります。

## 権限

ワークスペース内でのシンボル検索およびコード分析は、確認のメッセージが表示されることなく実行されます。ワークスペース外のファイルを対象とする操作には承認が必要です。

## 次の手順

- [組み込みツールの概要](https://kiro.dev/docs/tools/) - ツールカタログ一覧
- [言語サポートガイド](https://kiro.dev/docs/guides/languages-and-frameworks/) - 言語ごとのセットアップ


---

[← 前へ: Web tools](web.md) | [↑ 親ページ](index.md) | [次へ →: Configuration scopes](../configuration.md)
