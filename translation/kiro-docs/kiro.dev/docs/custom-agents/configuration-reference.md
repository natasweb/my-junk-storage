# Agent configuration reference

> 元URL: https://kiro.dev/docs/custom-agents/configuration-reference/  
> 最終取り込み日: 2026-09-05  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

このリファレンスでは、IDE 1.0 および CLI 3.0 のエージェント設定形式について説明しています。古いエージェント設定がある場合は、[`/upgrade-agent`](https://kiro.dev/docs/custom-agents/configuration-reference/#upgrading-from-older-configs)コマンドを使用して移行してください。

## バージョンの比較

**IDE 1.0 / CLI 3.0 の新機能:**

| フィールド | 説明 |
| --- | --- |
| `permissions` | 機能ベースのアクセス制御ルール（`toolsSettings` に代わるもの） |
| `excludedTools` | `tools`で許可されている場合でも、特定のツールを除外する |
| `includeMcpJson` | ワークスペースのMCPサーバーを自動的に含める |
| `includePowers` | IDEにインストールされたPowersを自動的に含める |
| `welcomeMessage` | セッション開始時のカスタム挨拶 |
| `resources` （拡張） | `file://`に加え、`skill://`および`knowledgeBase`に対応しました |
| Markdown 形式 (`.md`) | 設定用のフロントマター、システムプロンプト用の本文 |
| `tools`内のタグ | 短縮名：`read`、`write`、`shell`、`web`、`@builtin`、`*` |

**非推奨:**

| フィールド | 置換 |
| --- | --- |
| `toolsSettings` (shell/write ルール) | `permissions.rules` を、機能ベースのパターンに置き換える |

**変更なし:** `name`、`description`、`prompt`、`model`、`mcpServers`、`toolAliases`、`allowedTools`、`keyboardShortcut`、`hooks` (CLIのみ - IDEはこのフィールドを無視します)

古い設定ファイル内のサポート対象フィールドを移行するには、[`/upgrade-agent`](https://kiro.dev/docs/custom-agents/configuration-reference/#upgrading-from-older-configs) を使用してください。

## フィールドリファレンス

すべてのエージェント設定ファイルには、以下のセクションを含めることができます:

- [`name`](https://kiro.dev/docs/custom-agents/configuration-reference/#name-field) - エージェント名（オプション。指定がない場合はファイル名から派生されます）。
- [`description`](https://kiro.dev/docs/custom-agents/configuration-reference/#description-field) - エージェントの説明。
- [`prompt`](https://kiro.dev/docs/custom-agents/configuration-reference/#prompt-field) - エージェントに関する概要。
- [`mcpServers`](https://kiro.dev/docs/custom-agents/configuration-reference/#mcpservers-field) - エージェントがアクセス可能なMCPサーバー。
- [`tools`](https://kiro.dev/docs/custom-agents/configuration-reference/#tools-field) - エージェントが利用可能なツール。
- [`toolAliases`](https://kiro.dev/docs/custom-agents/configuration-reference/#toolaliases-field) - 名称の競合に対処するためのツール名の再マッピング。
- [`allowedTools`](https://kiro.dev/docs/custom-agents/configuration-reference/#allowedtools-field) - プロンプトなしで利用できるツール。
- [`permissions`](https://kiro.dev/docs/custom-agents/configuration-reference/#permissions-field) - 機能ベースのインラインアクセス制御ルール。
- [`toolsSettings`](https://kiro.dev/docs/custom-agents/configuration-reference/#toolssettings-field) - ツールごとの設定（シェル/ファイルシステムルールでは非推奨）。
- [`resources`](https://kiro.dev/docs/custom-agents/configuration-reference/#resources-field) - エージェントが利用可能なリソース。
- [`hooks`](https://kiro.dev/docs/custom-agents/configuration-reference/#hooks-field) - 特定のトリガーポイントで実行されるコマンド。
- [`includeMcpJson`](https://kiro.dev/docs/custom-agents/configuration-reference/#includemcpjson-field) - mcp.json ファイルから MCP サーバーを含めるかどうか。
- [`model`](https://kiro.dev/docs/custom-agents/configuration-reference/#model-field) - このエージェントで使用するモデルID。
- [`keyboardShortcut`](https://kiro.dev/docs/custom-agents/configuration-reference/#keyboardshortcut-field) - このエージェントに素早く切り替えるためのキーボードショートカット。
- [`welcomeMessage`](https://kiro.dev/docs/custom-agents/configuration-reference/#welcomemessage-field) - このエージェントに切り替えた際に表示されるメッセージ。

## 「名前」フィールド

「`name`」フィールドは、エージェントの名前を指定します。これは識別および表示の目的で使用されます。

json

```json
{
  "name": "aws-expert"
}
```

## 説明フィールド

`description`フィールドには、エージェントの機能に関する説明が記載されます。これは主に人間が読みやすいようにするためのものですが、ユーザーが異なるエージェントを区別するのにも役立ちます。

json

```json
{
  "description": "An agent specialized for AWS infrastructure tasks"
}
```

## 「Prompt」フィールド

`prompt`フィールドは、システムプロンプトと同様に、エージェントに大まかな文脈を提供することを目的としています。インラインテキストと、外部ファイルを参照するためのfile:// URIの両方をサポートしています。

### インライン・プロンプト

json

```json
{
  "prompt": "You are an expert AWS infrastructure specialist"
}
```

### ファイル URI プロンプト

`file://` URI を使用して外部ファイルを参照できます。これにより、長くて複雑なプロンプトを別ファイルに保存して整理やバージョン管理を容易にしつつ、エージェントの設定をすっきりとした読みやすい状態に保つことができます。

json

```json
{
  "prompt": "file://./my-agent-prompt.md"
}
```

#### ファイル URI のパス解決

- **相対パス**：エージェント設定ファイルのディレクトリを基準に解決されます
  - `"file://./prompt.md"` - エージェント設定ファイルと同じディレクトリ内の ``prompt.md``
  - `"file://../shared/prompt.md"` - 親ディレクトリ内の ``prompt.md``
- **絶対パス**：そのまま使用されます
  - `"file:///home/user/prompts/agent.md"` - ファイルへの絶対パス

#### ファイル URI の例

json

```json
{
  "prompt": "file://./prompts/aws-expert.md"
}
```

json

```json
{
  "prompt": "file:///Users/developer/shared-prompts/rust-specialist.md"
}
```

## McpServers フィールド

`mcpServers`フィールドは、エージェントがアクセスできるModel Context Protocol (MCP) サーバーを指定します。各サーバーは、コマンドとオプションの引数で定義されます。

json

```json
{
  "mcpServers": {
    "fetch": {
      "command": "fetch3.1",
      "args": []
    },
    "git": {
      "command": "git-mcp",
      "args": [],
      "env": {
        "GIT_CONFIG_GLOBAL": "/dev/null"
      },
      "timeout": 120000
    }
  }
}
```

各 MCP サーバーの設定には、以下を含めることができます:

- `command` (ローカルサーバーの場合、必須)：MCPサーバーを起動するために実行するコマンド
- `url` （リモートサーバー用）：HTTPエンドポイント。認証が必要なエンドポイントの場合は、オプションで`headers`を指定します
- `args` （オプション）：コマンドに渡す引数
- `env` (オプション)：サーバー用に設定する環境変数。値は `${VAR}` 構文をサポートしており、実行時に展開されるため、設定ファイルに機密情報を記載する必要がありません
- `timeout` （オプション）：stdio サーバーの接続ハンドシェイクタイムアウト（ミリ秒単位）（デフォルト：60000）
- `requestTimeout` (オプション): 1回の呼び出しあたりのリクエストタイムアウト（ミリ秒単位）（デフォルト: 120000）
- `oauth` (オプション): HTTP ベースの MCP サーバー用の OAuth 設定
  - `clientId` (オプション): ダイナミック・クライアント登録 (DCR) が失敗した際のフォールバックとして使用される、事前登録済みの OAuth クライアント ID。DCR をサポートせず、手動でのアプリ登録を通じて OAuth 認証情報を発行する Slack、GitHub、Figma などのサービスでは必須です。
  - `redirectUri` (オプション): OAuthフロー用のカスタムリダイレクトURI（例: "127.0.0.1:7778"）
- `oauthScopes` (オプション): 要求する OAuth スコープの配列（例：`["read", "write"]`）。これはサーバーエントリの最上位フィールドであり、`oauth` と同階層に位置し、その内部にネストされることはありません。

### OAuthの設定

OAuth 認証を必要とする HTTP ベースの MCP サーバーの場合、OAuth スコープを次のように設定できます：

json

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.github.com/mcp",
      "oauth": {
        "redirectUri": "127.0.0.1:8080"
      },
      "oauthScopes": ["repo", "user"]
    }
  }
}
```

OAuthスコープに関連するエラーが発生した場合は、MCPサーバーの設定内でスコープ要件をバイパスするために、空の配列を設定できます：

json

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.github.com/mcp",
      "oauth": {
        "redirectUri": "127.0.0.1:8080"
      },
      "oauthScopes": []
    }
  }
}
```

事前登録済みのOAuthアプリを必要とするサービスについては、`oauth.clientId`をアプリのIDに設定してください：

json

```json
{
  "mcpServers": {
    "slack": {
      "type": "http",
      "url": "https://mcp.slack.com/mcp",
      "oauth": {
        "clientId": "your-slack-app-client-id"
      },
      "oauthScopes": ["search:read", "channels:read"]
    }
  }
}
```

## [Tools] フィールド

「`tools`」フィールドには、エージェントが使用可能なすべてのツールが一覧表示されます。ツールには、組み込みツールおよびMCPサーバーからのツールが含まれます。

- 組み込みツールは、その名前で指定されます（例: `read`、`shell`）
- MCPサーバーのツールには、「`@`」という接頭辞に続いてサーバー名が付けられます（例：`@git`）
- MCPサーバー上の特定のツールを指定するには、`@server_name/tool_name` を使用します。
- 利用可能なすべてのツール（組み込みツールおよびMCPサーバーからのツールの両方）を含めるには、`*`を特別なワイルドカードとして使用します
- すべての組み込みツールを含めるには、`@builtin` を使用します
- `@server_name` を使用すると、特定の MCP サーバー上のすべてのツールを含めることができます

このフィールドではカテゴリタグも使用できます。各タグは関連する機能をグループ化するため、個々のツールを列挙する必要はありません：

| タグ | 含まれる内容 |
| --- | --- |
| `read` | ファイルの読み取り、ディレクトリの一覧表示、検索 |
| `write` | ファイルへの書き込み、編集、削除 |
| `shell` | コマンドの実行およびプロセス管理 |
| `web` | Webフェッチ |
| `subagent` | サブエージェントへの委任 |
| `knowledge` | ナレッジベースツール |
| `todo_list` | タスクの追跡 |
| `@mcp` | mcp.json に含まれるすべての MCP ツール |
| `@builtin` | すべての組み込みツール |
| `*` | すべて |

あるカテゴリの下で新しいツールがリリースされると、そのタグを使用しているエージェントは自動的にそれらを取り込みます。

json

```json
{
  "tools": [
    "read",
    "write",
    "shell",
    "@git",
    "@rust-analyzer/check_code"
  ]
}
```

利用可能なすべてのツールを含めるには、次のように指定します:

json

```json
{
  "tools": ["*"]
}
```

## ToolAliasesフィールド

`toolAliases`フィールドは、ツール名の再マッピングを可能にする高度な機能です。これは主に、異なるMCPサーバー間のツール名の一致問題を解決したり、特定のツールに対してより直感的な名前を付けたりするために使用されます。

たとえば、`@github-mcp` サーバーと `@gitlab-mcp` サーバーの両方で「`get_issues`」という名前のツールが提供されている場合、名称の競合が発生します。`toolAliases` を使用することで、これらを区別することができます:

json

```json
{
  "toolAliases": {
    "@github-mcp/get_issues": "github_issues",
    "@gitlab-mcp/get_issues": "gitlab_issues"
  }
}
```

この設定により、`get_issues` での名称の競合を回避し、エージェントからは `github_issues` と `gitlab_issues` としてツールが利用可能になります。

また、エイリアスを使用することで、頻繁に使用するツールに対して、より短く、あるいはより直感的な名前を付けることもできます：

json

```json
{
  "toolAliases": {
    "@aws-cloud-formation/deploy_stack_with_parameters": "deploy_cf",
    "@kubernetes-tools/get_pod_logs_with_namespace": "pod_logs"
  }
}
```

キーは元のツール名（MCPツールの場合はサーバープレフィックスを含む）で、値は使用する新しい名前です。

## AllowedTools フィールド

`allowedTools`フィールドは、ユーザーに許可を求めずに使用できるツールを指定します。これは、不正なツールの使用を防ぐためのセキュリティ機能です。

json

```json
{
  "allowedTools": [
    "read",
    "write",
    "@git/git_status",
    "@server/read_*",
    "@fetch"
  ]
}
```

ツールへの許可は、以下のいくつかのパターンを用いて設定できます：

### 完全一致

- **組み込みツール**：`"read"`、`"shell"`、`"knowledge"`
- **特定のMCPツール**：`"@server_name/tool_name"`（例：`"@git/git_status"`）
- **MCPサーバーのすべてのツール**：`"@server_name"`（例：`"@fetch"`）

### ワイルドカードパターン

`allowedTools`フィールドでは、`*`および`?`を使用したglob形式のワイルドカードパターンがサポートされています：

#### MCPツールパターン

- **ツールプレフィックス**: `"@server/read_*"` - `@server/read_file`、`@server/read_config` に一致
- **ツール接尾辞**: `"@server/*_get"` - `@server/issue_get`、`@server/data_get` に一致
- **サーバーパターン**：`"@*-mcp/read_*"` - `@git-mcp/read_file`、`@db-mcp/read_data` に一致
- **パターンサーバー上の任意のツール**: `"@git-*/*"` - `git-*` に一致するサーバー上の任意のツールに一致します

必要に応じて、ネイティブツールの名前前に名前空間 `@builtin` を付けることもできます。

### 例

json

```json
{
  "allowedTools": [
    "read",
    "knowledge",
    "@server/specific_tool",

    "r*",
    "w*",
    "@builtin",

    "@server/api_*",
    "@server/read_*",
    "@git-server/get_*_info",
    "@*/status",

    "@fetch",
    "@git-*"
  ]
}
```

### パターンマッチングのルール

- **`*`** 任意の文字列（空文字列を含む）に一致します。
- **`?`** 1 文字に厳密に一致します
- **完全一致は**パターンよりも優先されます
- **サーバーレベルの権限**（`@server_name`）は、そのサーバーのすべてのツールを許可します
- **大文字と小文字を区別する**マッチング

「`tools`」フィールドとは異なり、「`allowedTools`」フィールドでは、すべてのツールを許可するためのワイルドカード「`"*"`」はサポートされていません。ツールを許可するには、特定のパターンまたはサーバーレベルの権限を使用する必要があります。

## ToolsSettingsフィールド

`toolsSettings`フィールドは、ツールごとの設定を提供します。以前のバージョンでは、これはシェルコマンドの許可/拒否リストやファイルパスの制限に使用されていました。これらのユースケースは、現在`permissions`フィールドによって処理されています。

このフィールドは、MCPツール固有の設定については引き続きサポートされています：

json

```json
{
  "toolsSettings": {
    "@git/git_status": {
      "git_user": "$GIT_USER"
    },
    "subagent": {
      "availableAgents": ["reviewer", "tester"],
      "trustedAgents": ["reviewer"]
    }
  }
}
```

## Permissionsフィールド

`permissions`フィールドは、エージェントプロファイルに埋め込まれた、機能ベースのインラインポリシールールを提供します。これは、ツールが実行できる操作を制御するための従来の`toolsSettings`アプローチに代わるものです。

ルールは`permissions.yaml`ファイルと同じ構文を使用しますが（[「Permissions](https://kiro.dev/docs/permissions/)」を参照）、その適用範囲はこのエージェントのみに限定されます。

json

```json
{
  "permissions": {
    "rules": [
      { "capability": "shell", "match": ["npm *", "git *"], "effect": "allow" },
      { "capability": "fs_write", "match": ["src/**", "tests/**"], "effect": "allow" },
      { "capability": "shell", "match": ["rm -rf *", "sudo *"], "effect": "deny" }
    ]
  }
}
```

各ルールには以下が含まれます：

| フィールド | 説明 |
| --- | --- |
| `capability` | 制御可能な機能：`fs_read`、`fs_write`、`shell`、`web_fetch`、`web_search`、`mcp`、`subagent`、`all` |
| `match` | ルールの適用範囲を指定するグロブパターン（fsの場合はファイルパス、shellの場合はコマンドのプレフィックス、MCPの場合はサーバー名またはツール名） |
| `effect` | `allow` (黙って処理する)、`ask` (ユーザーに確認を求める)、または `deny` (常にブロックする) |
| `exclude` | 一致してはならないオプションのグロブパターン |

エージェントスコープの権限は、3つの効果すべて（`allow`、`ask`、`deny`）をサポートしています。deny-overrides アルゴリズムが適用されます。つまり、どのスコープであれ、`deny` の方が優先され、他の場所の `allow` ルールは無視されます。

## リソースフィールド

`resources`フィールドは、エージェントにローカルリソースへのアクセス権を与えます。リソースには、ファイル、スキル、ナレッジベースなどが含まれます。

json

```json
{
  "resources": [
    "file://README.md",
    "file://.kiro/steering/**/*.md",
    "skill://.kiro/skills/**/SKILL.md"
  ]
}
```

リソースは、URIスキームを通じてさまざまなタイプをサポートしています：

- `file://` - 起動時にコンテキストに直接読み込まれるファイル
- `skill://` - 起動時にメタデータが読み込まれ、コンテンツ全体はオンデマンドで読み込まれるスキル

いずれも以下をサポートしています：

- 特定のパス: `file://README.md` または `skill://my-skill.md`
- グロブパターン: `file://.kiro/**/*.md` または `skill://.kiro/skills/**/SKILL.md`
- 絶対パスまたは相対パス

### ファイルリソース

ファイルリソースは、エージェントの起動時にエージェントのコンテキストに直接読み込まれます。エージェントが常に必要とするコンテンツにはこれらを使用してください。

json

```json
{
  "resources": [
    "file://README.md",
    "file://docs/**/*.md"
  ]
}
```

### スキルリソース

スキルは段階的に読み込まれます。起動時にはメタデータ（名前と説明）のみが読み込まれ、エージェントが必要と判断した際にオンデマンドで完全なコンテンツが読み込まれます。これにより、コンテキストを軽量に保ちつつ、エージェントが豊富なドキュメントにアクセスできるようになります。

スキルファイルは、`name`および`description`を含むYAMLフロントマターで始まる必要があります：

markdown

```markdown
---
name: dynamodb-data-modeling
description: Guide for DynamoDB data modeling best practices. Use when designing or analyzing DynamoDB schema.
---

# DynamoDB Data Modeling

... full content here ...
```

json

```json
{
  "resources": [
    "skill://.kiro/skills/**/SKILL.md"
  ]
}
```

エージェントが完全なコンテンツをいつ読み込むべきかを確実に判断できるよう、具体的な説明を記述してください。

### ナレッジベースリソース

ナレッジベースリソースを使用すると、エージェントはインデックス化されたドキュメントやコンテンツを検索できます。数百万トークン規模のインデックス化されたコンテンツと増分読み込みに対応しているため、エージェントは大規模なドキュメントセットを効率的に検索できます。

json

```json
{
  "resources": [
    {
      "type": "knowledgeBase",
      "source": "file://./docs",
      "name": "ProjectDocs",
      "description": "Project documentation and guides",
      "indexType": "best",
      "autoUpdate": true
    }
  ]
}
```

**フィールド:**

| フィールド | 必須 | 説明 |
| --- | --- | --- |
| `type` | はい | `"knowledgeBase"` である必要があります |
| `source` | はい | インデックスへのパス。ローカルパスには「`file://`」というプレフィックスを使用してください |
| `name` | はい | ナレッジベースの表示名 |
| `description` | いいえ | コンテンツの概要 |
| `indexType` | いいえ | インデックス作成戦略：`"best"`（デフォルト、高品質）または `"fast"`（インデックス作成が高速） |
| `autoUpdate` | なし | エージェントの生成時に再インデックス。デフォルト：`false` |

**使用例：**

- エージェント間でチームドキュメントを共有する
- エージェントにプロジェクト固有のコンテキスト（仕様書、決定事項、会議議事録）へのアクセス権を付与する
- 大規模なコードベースやドキュメントをインデックス化
- `autoUpdate: true` を使用してエージェントの知識を最新の状態に保つ

## フックフィールド

`hooks`フィールドは、エージェントのライフサイクルやツールの実行中に特定のトリガーポイントで実行されるコマンドを定義します。

CLI と IDE の両方で、この形式で記述されたフックが受け入れられるため、Kiro CLI 用に構築されたエージェントプロファイルは、フックを書き換えることなく IDE で読み込むことができます。

フックの動作、入出力形式、および例に関する詳細については、[フックのドキュメント](https://kiro.dev/docs/hooks/)を参照してください。

json

```json
{
  "hooks": {
    "agentSpawn": [
      {
        "command": "git status"
      }
    ],
    "userPromptSubmit": [
      {
        "command": "ls -la"
      }
    ],
    "preToolUse": [
      {
        "matcher": "execute_bash",
        "command": "{ echo \"$(date) - Bash command:\"; cat; echo; } >> /tmp/bash_audit_log"
      },
      {
        "matcher": "use_aws",
        "command": "{ echo \"$(date) - AWS CLI call:\"; cat; echo; } >> /tmp/aws_audit_log"
      }
    ],
    "postToolUse": [
      {
        "matcher": "fs_write",
        "command": "cargo fmt --all"
      }
    ]
  }
}
```

各フックは以下のように定義されます：

- `command` (必須): 実行するコマンド
- `matcher` (オプション): ``preToolUse`` および ``postToolUse`` フック用のツール名に一致させるパターン。フックマッチャーは、簡略化された名前ではなく、内部ツール名 (`fs_read`、`fs_write`、`execute_bash`、`use_aws`) を使用します。利用可能なツール名については、[組み込みツールのドキュメント](https://kiro.dev/docs/reference/built-in-tools/)を参照してください。

利用可能なフックトリガー：

- `agentSpawn`：エージェントの初期化時にトリガーされます。
- `userPromptSubmit`: ユーザーがメッセージを送信したときにトリガーされます。
- `preToolUse`: ツールが実行される前にトリガーされます。ツールの使用をブロックすることができます。
- `postToolUse`: ツールの実行後にトリガーされます。
- `stop`: アシスタントが応答を完了したときにトリガーされます。

## includeMcpJson フィールド

`includeMcpJson` フィールドは、MCP 設定ファイル（グローバル用は `~/.kiro/settings/mcp.json`、ワークスペース用は `<cwd>/.kiro/settings/mcp.json`）で定義された MCP サーバーを含めるかどうかを決定します。

json

```json
{
  "includeMcpJson": true
}
```

`true` に設定すると、エージェントは、エージェントの `mcpServers` フィールドで定義されているサーバーに加え、グローバルおよびローカルの設定で定義されているすべての MCP サーバーにアクセスできるようになります。

## Model フィールド

`model` フィールドは、このエージェントで使用するモデル ID を指定します。指定されていない場合、エージェントはデフォルトのモデルを使用します。

json

```json
{
  "model": "claude-sonnet-4"
}
```

モデル ID は、Kiro のモデルサービスによって返される利用可能なモデルのいずれかと一致している必要があります。利用可能なモデルは、アクティブなチャットセッションで ``/model`` コマンドを使用することで確認できます。

指定されたモデルが利用できない場合、エージェントはデフォルトのモデルに切り替わり、警告を表示します。

## KeyboardShortcut フィールド

`keyboardShortcut`フィールドは、チャットセッション中にこのエージェントに素早く切り替えるためのキーボードショートカットを設定します。

json

```json
{
  "keyboardShortcut": "ctrl+a"
}
```

ショートカットは、修飾子とキーで構成され、`+` で区切られます。

**修飾キー**（オプション）：

- `ctrl` - Control キー
- `shift` - Shiftキー

**キー**:

- 単一の文字: `a-z`（大文字・小文字を区別しない）
- 1桁の数字：`0-9`

**例**:

json

```json
"keyboardShortcut": "ctrl+a"
"keyboardShortcut": "shift+b"
```

**動作の切り替え:**

キーボードショートカットを押したとき：

- 別のエージェントにいる場合：このエージェントに切り替わります
- すでにこのエージェントにいる場合：前のエージェントに戻る

**競合時の処理:**

複数のエージェントで同じキーボードショートカットが設定されている場合、警告が記録され、そのショートカットは無効化されます。この場合は、`/agent swap` を使用して手動で切り替えてください。

## WelcomeMessage フィールド

`welcomeMessage`フィールドは、このエージェントに切り替えた際に表示されるメッセージを指定します。

json

```json
{
  "welcomeMessage": "What would you like to build today?"
}
```

このメッセージは、エージェント切り替えの確認後に表示され、ユーザーにそのエージェントの目的を理解してもらうのに役立ちます。

## デフォルトのリソース継承を無効にする

デフォルトでは、カスタムエージェントは、独自に構成されたリソースに加えて、デフォルトのリソース（ステアリングファイル、スキル、および`AGENTS.md`）を継承します。この動作は、`chat.disableInheritingDefaultResources`というCLI設定で無効にできます。

| プロパティ | 値 |
| --- | --- |
| 設定キー | `chat.disableInheritingDefaultResources` |
| 型 | ブール値 |
| デフォルト | `false` (カスタムエージェントはデフォルトのリソースを継承します) |
| スコープ | グローバル、またはワークスペースで上書き可能 |

CLI 経由で設定するには：

bash

```bash
kiro-cli settings chat.disableInheritingDefaultResources true
```

または、ワークスペースにスコープを限定するには:

bash

```bash
kiro-cli settings --workspace chat.disableInheritingDefaultResources true
```

`true`に設定すると、カスタム（ユーザー定義）エージェントは、そのコンテキストにおいてデフォルトのステアリング、スキル、または`AGENTS.md`を受け取らなくなります。組み込みエージェントは、この設定にかかわらず、常にデフォルトのリソースを継承します。

## 完全な例

json

```json
{
  "name": "aws-rust-agent",
  "description": "A specialized agent for AWS and Rust development tasks",
  "mcpServers": {
    "fetch": {
      "command": "fetch3.1",
      "args": []
    },
    "git": {
      "command": "git-mcp",
      "args": []
    }
  },
  "tools": [
    "read",
    "write",
    "shell",
    "@git",
    "@fetch/fetch_url"
  ],
  "toolAliases": {
    "@git/git_status": "status",
    "@fetch/fetch_url": "get"
  },
  "allowedTools": [
    "read",
    "@git/git_status"
  ],
  "permissions": {
    "rules": [
      { "capability": "shell", "match": ["cargo *", "git *"], "effect": "allow" },
      { "capability": "fs_write", "match": ["src/**", "tests/**", "Cargo.toml"], "effect": "allow" },
      { "capability": "shell", "match": ["rm -rf *"], "effect": "deny" }
    ]
  },
  "resources": [
    "file://README.md",
    "file://docs/**/*.md"
  ],
  "hooks": {
    "agentSpawn": [
      {
        "command": "git status"
      }
    ],
    "postToolUse": [
      {
        "matcher": "fs_write",
        "command": "cargo fmt --all"
      }
    ]
  },
  "includeMcpJson": true,
  "model": "claude-sonnet-4",
  "keyboardShortcut": "ctrl+r",
  "welcomeMessage": "Ready to help with AWS and Rust development!"
}
```

## ベストプラクティス

1. **制限的な設定から始める**：ツールへのアクセス権限は最小限に抑え、必要に応じて拡大していく
2. **明確な命名**：エージェントの目的がわかるような説明的な名前を使用する
3. **使用方法を文書化する**：チームメンバーがエージェントを理解できるよう、明確な説明を追加する
4. **バージョン管理**：エージェントの設定をプロジェクトのリポジトリに保存する
5. **徹底的にテストする**：共有する前に、ツールの権限が期待通りに機能することを確認する

### ローカルエージェントとグローバルエージェント

**ローカルエージェントの使用用途：**

- プロジェクト固有の設定
- プロジェクトファイルやツールを必要とするエージェント
- 独自の要件がある開発環境
- バージョン管理を介したチーム内での共有

**グローバルエージェントを使用するケース：**

- プロジェクトを横断する汎用エージェント
- 個人の生産性向上用エージェント
- プロジェクト固有のコンテキストを持たないエージェント
- よく使われるツールとワークフロー

### セキュリティのベストプラクティス

- `allowedTools` を慎重に確認する
- ワイルドカードよりも具体的なパターンを使用する
- `permissions.rules` を使用して機密性の高い操作を制限する
- まず安全な環境でエージェントをテストする

#### ツールの権限を定義する

デフォルトでは、Kiroエージェントは読み取り専用ツールにのみアクセスできます。`allowedTools`で明示的に有効にするか、実行時に承認しない限り、書き込み操作は許可されません。

書き込みツール（`write`、`shell`、または書き込み機能を持つ MCP ツールなど）を有効にすると、エージェントはユーザーアカウントと同じファイルシステム権限で動作します。つまり、

- エージェントは、`~/.kiro` 配下のすべてのファイル（スキルコンテキストファイル、ステアリングファイル、MCP サーバー構成（`mcp.json`）、およびその他のエージェント構成を含む）を読み取り、変更できます。
- インストールされたすべてのスキルおよびリソースは、同じ権限を共有します。個々のスキル間の分離は存在せず、書き込みツールが有効になっている場合、エージェントはどのスキルのコンテキストでも読み取りや変更を行うことができます。
- スキル自体はコードを実行することはできません。スキルは、エージェントに指示を与えるテキストファイルです。ただし、`shell` のような書き込みツールが許可されている場合、エージェントは読み込まれたスキル内で参照されているコマンドを実行する可能性があります。

書き込みツールを使用する際のリスクを軽減するには：

- 必要な特定の書き込みツールのみを有効にしてください（例：`"write"` は有効にするが、`"shell"` は無効にする）。
- `permissions.rules` を使用して、`match` パターンを厳格に指定し、パスおよびコマンドの制限を定義してください。
- スキルやリソースをインストールする前に、特に信頼できないソースからのものについては、内容を精査してください。
- `preToolUse` [のフック](https://kiro.dev/docs/hooks/)を使用して、機密性の高い操作を監査またはブロックしてください。

## 古い設定からのアップグレード

IDE 1.0 または CLI 3.0 より前に作成されたエージェント設定がある場合、`/upgrade-agent` は移行が必要な設定を特定し、元の設定をバックアップして、サポートされているフィールドをその場で変換します。

bash

```bash
# Scan agents and open the selection menu
/upgrade-agent

# Review previously upgraded agents and any conversion warnings
/upgrade-agent diagnostics
```

このコマンドは、ワークスペース内の ``.kiro/agents/`` およびグローバルな ``~/.kiro/agents/`` をスキャンし、スコープごとにグループ化された選択メニューを表示します。アップグレードが必要なエージェントのみが表示されます。

V3の`/agent`ピッカーでは、レガシーCLI 2.xのプロファイルも引き続き表示され、選択可能です。`/upgrade-agent`は、オブジェクト形式のフックを、V3で必要とされる配列形式に変換します。

変更前：

json

```json
{
  "hooks": {
    "agentSpawn": {
      "command": "git status"
    }
  }
}
```

変更後:

json

```json
{
  "hooks": {
    "agentSpawn": [
      {
        "command": "git status"
      }
    ]
  }
}
```

### 変換される内容

| 旧パターン | 新しい等価表現 |
| --- | --- |
| `toolsSettings.shell.allowedCommands` | `permissions.rules` `capability: shell`、`effect: allow` を使用 |
| `toolsSettings.shell.deniedCommands` | `permissions.rules` `capability: shell`、`effect: deny` を使用する場合 |
| `toolsSettings.write.allowedPaths` | `permissions.rules` `capability: fs_write`、`effect: allow` を使用する場合 |
| `allowedTools` エントリ | 機能レベルの許可ルール |
| ツール名（`fs_read`、`execute_bash`） | タグ（`read`、`shell`） - どちらも引き続き機能します |
| `autoAllowReadonly` | 読み取り専用シェルポリシールール |
| オブジェクト形式のフック | V3スキーマで必須の配列形式のフック |

元のファイルは `<filename>.json.bak` にバックアップされています。元に戻すには、バックアップファイルの名前を元の名前に変更してください。

診断やトラブルシューティングを含む完全なアップグレードガイドについては、[「CLI 3.0の新機能 - エージェント設定」を](https://kiro.dev/docs/cli/v3/agent-config/)参照してください。

## 次の手順

- [カスタムエージェントの作成](https://kiro.dev/docs/custom-agents/creating/)
- [例](https://kiro.dev/docs/custom-agents/examples/) - 実際のエージェント設定
- [トラブルシューティング](https://kiro.dev/docs/custom-agents/troubleshooting/) - よくある問題の解決
- [フックのドキュメント](https://kiro.dev/docs/hooks/)


---

[← 前へ: Creating custom agents](creating.md) | [↑ 親ページ](index.md) | [次へ →: Invoking as sub-agents](subagents.md)
