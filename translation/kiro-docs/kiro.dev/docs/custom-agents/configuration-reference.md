# Agent configuration reference

> 元URL: https://kiro.dev/docs/custom-agents/configuration-reference/  
> 最終取り込み日: 2026-11-01  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

このリファレンスでは、IDE 1.0 および CLI V3 のエージェント設定形式について説明しています。古いエージェント設定がある場合は、[`/upgrade-agent`](https://kiro.dev/docs/custom-agents/configuration-reference/#upgrading-from-older-configs)コマンドを使用して移行してください。

## バージョン比較

**IDE 1.0 / CLI V3 の新機能:**

| フィールド | 説明 |
| --- | --- |
| `permissions` | 機能ベースのアクセス制御ルール（`toolsSettings` に代わるもの） |
| `excludedTools` | `tools`で許可されている場合でも、特定のツールを除外する |
| `includeMcpJson` | ワークスペースの MCP サーバーを自動的に含める |
| `includePowers` | IDEにインストールされた機能を自動的に含める |
| `welcomeMessage` | セッション開始時のカスタム挨拶 |
| `resources`（拡張版） | `file://`に加え、`skill://`および`knowledgeBase`に対応しました |
| Markdown 形式（`.md`） | 設定用のフロントマター、システムプロンプト用の本文 |
| `tools` 内のタグ | 短縮名：`read`、`write`、`shell`、`web`、`@builtin`、`*` |

**CLI 2.x 移行用フィールド：**

| フィールド | CLI V3での処理 |
| --- | --- |
| `toolsSettings` | `/upgrade-agent`は、サポートされているシェル、ファイル、および`subagent`の設定を`permissions.rules`および`tools`に変換します |
| `allowedTools` | `/upgrade-agent`はエントリを機能レベルの許可ルールに変換します。新しいプロファイルでは`permissions`を使用します |
| `toolAliases` | CLI V3 スキーマには含まれません。完全なツールセレクタを使用してください |
| `keyboardShortcut` | CLI V3 スキーマの一部ではありません |

**変更なし：**`name`、`description`、`prompt`、`model`、`mcpServers`

インラインの `hooks` には、現在の形状とレガシーの形状があります。[「フック」フィールドを](https://kiro.dev/docs/custom-agents/configuration-reference/#hooks-field)参照してください。サーフェス間の自動化には、スタンドアロンの[フックファイル](https://kiro.dev/docs/hooks/)を使用してください。

古い設定でサポートされているフィールドを移行するには、[`/upgrade-agent`](https://kiro.dev/docs/custom-agents/configuration-reference/#upgrading-from-older-configs) を使用してください。

## フィールドリファレンス

すべてのエージェント設定ファイルには、以下のセクションを含めることができます:

- [`name`](https://kiro.dev/docs/custom-agents/configuration-reference/#name-field) - エージェントの名前（オプション。指定がない場合はファイル名から派生）。
- [`description`](https://kiro.dev/docs/custom-agents/configuration-reference/#description-field) - エージェントの説明。
- [`prompt`](https://kiro.dev/docs/custom-agents/configuration-reference/#prompt-field) - エージェントに関する概要。
- [`mcpServers`](https://kiro.dev/docs/custom-agents/configuration-reference/#mcpservers-field) - エージェントがアクセスできる MCP サーバー。
- [`tools`](https://kiro.dev/docs/custom-agents/configuration-reference/#tools-field) - エージェントが利用可能なツール。
- [`toolAliases`](https://kiro.dev/docs/custom-agents/configuration-reference/#toolaliases-field) - CLI 2.x のレガシーなツール名の再マッピング。
- [`allowedTools`](https://kiro.dev/docs/custom-agents/configuration-reference/#allowedtools-field) - CLI 2.x のレガシー事前承認エントリ。これらを `permissions` に移行します。
- [`permissions`](https://kiro.dev/docs/custom-agents/configuration-reference/#permissions-field) - 機能ベースのインラインアクセス制御ルール。
- [`toolsSettings`](https://kiro.dev/docs/custom-agents/configuration-reference/#toolssettings-field) - CLI 2.x のレガシーなツールごとの設定。CLI V3 では無視されます。
- [`resources`](https://kiro.dev/docs/custom-agents/configuration-reference/#resources-field) - エージェントが利用可能なリソース。
- [`hooks`](https://kiro.dev/docs/custom-agents/configuration-reference/#hooks-field) - 特定のトリガーポイントで実行されるコマンド。
- [`includeMcpJson`](https://kiro.dev/docs/custom-agents/configuration-reference/#includemcpjson-field) - mcp.json ファイルから MCP サーバーを含めるかどうか。
- [`model`](https://kiro.dev/docs/custom-agents/configuration-reference/#model-field) - このエージェントに使用するモデル ID。
- [`keyboardShortcut`](https://kiro.dev/docs/custom-agents/configuration-reference/#keyboardshortcut-field) - CLI 2.x のレガシーなエージェント切り替えショートカット。
- [`welcomeMessage`](https://kiro.dev/docs/custom-agents/configuration-reference/#welcomemessage-field) - このエージェントに切り替える際に表示されるメッセージ。

## 「名前」フィールド

`name`フィールドは、エージェントの名前を指定します。これは、識別および表示の目的で使用されます。

json

```json
{
  "name": "aws-expert"
}
```

## 説明フィールド

`description`フィールドは、エージェントの機能に関する説明を提供します。これは主に人間が読みやすいようにするためのものであり、ユーザーが異なるエージェントを区別するのに役立ちます。

json

```json
{
  "description": "An agent specialized for AWS infrastructure tasks"
}
```

## プロンプトフィールド

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

- **相対パス**：エージェント設定ファイルのディレクトリを基準として解決されます
  - `"file://./prompt.md"` - エージェント設定ファイルと同じディレクトリ内の `prompt.md`
  - `"file://../shared/prompt.md"` - 親ディレクトリ内の `prompt.md`
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

`mcpServers`フィールドは、エージェントがアクセスできるモデルコンテキストプロトコル（MCP）サーバーを指定します。各サーバーは、コマンドとオプションの引数で定義されます。

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

- `command`（ローカルサーバーの場合は必須）：MCPサーバーを起動するために実行するコマンド
- `url`（リモートサーバー用）：HTTPエンドポイント。認証が必要なエンドポイントの場合は、オプションで`headers`を指定します
- `args`（オプション）：コマンドに渡す引数
- `env`（オプション）：サーバー用に設定する環境変数。値は `${VAR}` 構文をサポートし、実行時に展開されるため、設定ファイルに機密情報を記載する必要がありません
- `timeout`（オプション）：stdio および HTTP サーバーの接続ハンドシェイクタイムアウト（ミリ秒単位）（デフォルト：60000）
- `requestTimeout`（オプション）：リクエストごとのタイムアウト（ミリ秒単位）（デフォルト：120000）
- `oauth`（オプション）：HTTP ベースの MCP サーバー用の OAuth 設定
  - `clientId`（オプション）：動的クライアント登録（DCR）が失敗した際のフォールバックとして使用される、事前登録済みの OAuth クライアント ID。DCR をサポートせず、手動でのアプリ登録を通じて OAuth 認証情報を発行する Slack、GitHub、Figma などのサービスには必須です。
  - `redirectUri`（オプション）：OAuthフロー用のカスタムリダイレクトURI（例：「127.0.0.1:7778」）
- `oauthScopes`（オプション）：リクエストする OAuth スコープの配列（例：`["read", "write"]`）。これはサーバーエントリの最上位フィールドであり、`oauth` と同階層にあり、その内部にネストされてはいけません。

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

OAuthスコープに関連するエラーが発生した場合は、MCPサーバーの設定内で空の配列を指定することで、スコープ要件をバイパスできます：

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

## [ツール] フィールド

`tools`フィールドには、エージェントが使用可能なすべてのツールが一覧表示されます。ツールには、組み込みツールとMCPサーバーからのツールが含まれます。

- 組み込みツールは、その名前で指定されます（例：`read`、`shell`）。
- MCP サーバーのツールは、`@` をプレフィックスとして、その後にサーバー名が続きます（例：`@git`）
- MCPサーバー上の特定のツールを指定するには、`@server_name/tool_name` を使用します
- 利用可能なすべてのツール（組み込みツールとMCPサーバーからのツールの両方）を含めるには、`*`を特別なワイルドカードとして使用します
- すべての組み込みツールを含めるには、`@builtin` を使用します
- 特定の MCP サーバーのすべてのツールを含めるには、`@server_name` を使用します

カテゴリタグには、`read`、`write`、`shell`、`web`、`subagent` があります。`knowledge` などの組み込みツールは、ツール ID を直接指定して選択します。すべての MCP ツールには `@mcp`、すべての組み込みツールには `@builtin`、すべてを含めるには `*` を使用します。カテゴリの所属およびセレクタの動作に関する正式な情報については、[「組み込みツール」を](https://kiro.dev/docs/tools/#configuring-tool-access)参照してください。

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

利用可能なすべてのツールを含めるには、次のように指定します：

json

```json
{
  "tools": ["*"]
}
```

## ToolAliases フィールド

`toolAliases`フィールドはCLI 2.xのレガシーフィールドであり、CLI V3では適用されません。このセクションの例は古いプロファイルに関するものです。現在のプロファイルでは、完全なツールセレクタを使用する必要があります。

たとえば、`@github-mcp` サーバーと `@gitlab-mcp` サーバーの両方が `get_issues` というツールを提供している場合、名前が重複してしまいます。`toolAliases` を使用することで、それらを区別できます:

json

```json
{
  "toolAliases": {
    "@github-mcp/get_issues": "github_issues",
    "@gitlab-mcp/get_issues": "gitlab_issues"
  }
}
```

この設定により、`get_issues` での名前衝突を回避し、エージェントからは `github_issues` および `gitlab_issues` としてツールが利用可能になります。

また、エイリアスを使用することで、頻繁に使用するツールに対して、より短い、あるいは直感的な名前を付けることもできます：

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

`allowedTools`フィールドはCLI 2.xに属します。新しいCLI V3プロファイルでは、機能ベースの`permissions.rules`が使用されます。`permissions`ブロックを含まない`allowedTools`を含むCLI 2.x JSONプロファイルは、`/upgrade-agent`によって変換されるまでV3によってスキップされます。以下のパターンは、レガシーフィールドについて記載したものです。

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

以下のパターンを使用してツールの使用を許可できます：

### 完全一致

- **組み込みツール**：`"read"`、`"shell"`、`"knowledge"`
- **特定の MCP ツール**：`"@server_name/tool_name"`（例：`"@git/git_status"`）
- **MCP サーバーのすべてのツール**：`"@server_name"`（例：`"@fetch"`）

### ワイルドカードパターン

`allowedTools`フィールドでは、`*`および`?`を使用したglob形式のワイルドカードパターンがサポートされています:

#### MCP ツールのパターン

- **ツールプレフィックス**：`"@server/read_*"` - `@server/read_file`、`@server/read_config` に一致
- **ツール接尾辞**：`"@server/*_get"` - `@server/issue_get`、`@server/data_get` に一致
- **サーバーパターン**：`"@*-mcp/read_*"` - `@git-mcp/read_file`、`@db-mcp/read_data` に一致
- **パターンサーバー上の任意のツール**：`"@git-*/*"` - `git-*` に一致するサーバー上の任意のツールに一致

オプションで、ネイティブツールに名前空間 `@builtin` をプレフィックスとして付けることもできます。

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

- **`*`**は、任意の文字列（文字がない場合も含む）に一致します。
- **`?`**は、1文字に厳密に一致します
- **完全一致は**パターンよりも優先されます
- **サーバーレベルの権限**（`@server_name`）**は、**そのサーバーからのすべてのツールを許可します
- **大文字と小文字を区別する**マッチング

`tools` フィールドとは異なり、`allowedTools` フィールドでは、すべてのツールを許可するための `"*"` ワイルドカードはサポートされていません。ツールを許可するには、特定のパターンまたはサーバーレベルの権限を使用する必要があります。

## ToolsSettings フィールド

`toolsSettings`フィールドは、CLI 2.xにおいて、シェルコマンドの許可リストおよび拒否リスト、ファイルパスの制限、サブエージェントへのアクセスなど、ツールごとの設定を提供します。CLI V3では`toolsSettings`は無視されます。`permissions`ブロックを含まない`toolsSettings`を含むCLI 2.xのJSONプロファイルは、`/upgrade-agent`によって変換されるまでV3によってスキップされます。

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

CLI V3 では、`subagent` の設定を、代わりに `tools` および `permissions` で記述してください。[「サブエージェントへのアクセス設定」を](https://kiro.dev/docs/custom-agents/subagents/#configuring-sub-agent-access)参照してください。

json

```json
{
  "tools": ["read", "write", "shell", "web", "subagent/reviewer", "subagent/tester"],
  "permissions": {
    "rules": [
      { "capability": "subagent", "match": ["reviewer"], "effect": "allow" }
    ]
  }
}
```

## Permissions フィールド

`permissions`フィールドは、エージェントプロファイルに埋め込まれたインラインの機能ベースのポリシールールを提供します。これは、ツールが実行できる操作を制御するための従来の`toolsSettings`アプローチに代わるものです。

ルールは`permissions.yaml`ファイルと同じ構文を使用しますが（[「権限」を](https://kiro.dev/docs/permissions/)参照）、その適用範囲はこのエージェントのみに限定されます。

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
| `capability` | 制御対象の機能：`fs_read`、`fs_write`、`shell`、`web_fetch`、<img id="5"/>、`web_search`、`mcp`、`subagent`、`skill`、`power`、`context`、`diagnostics`、`sandbox_network`、`all`、`builtin`、`filesystem`[、および](https://kiro.dev/docs/permissions/#how-it-works) |
| `match` | ルールの適用範囲を指定するグロブパターン（fs ではファイルパス、shell ではコマンドのプレフィックス、MCP ではサーバー／ツール名） |
| `effect` | `allow`（黙って処理を続行）、`ask`（ユーザーに確認を求める）、または`deny`（常にブロックする） |
| `exclude` | 一致してはならないオプションのグロブパターン |

エージェントスコープの権限は、3つの効果すべて（`allow`、`ask`、`deny`）をサポートしています。「拒否優先（deny-overrides）」アルゴリズムが適用されます。つまり、どのスコープ内の`deny`であっても、他の場所にある`allow`ルールにかかわらず優先されます。

このエージェントがCLI V3またはIDEでサブエージェントとして実行される場合、Kiroは各ツール呼び出しについても親セッションのポリシーと照合し、より厳格な結果が優先されます。エージェントの`allow`ルールは、親セッションで許可されていない権限を付与することはありません。「[サブエージェントの権限」](https://kiro.dev/docs/custom-agents/subagents/#sub-agent-permissions)を参照してください。

## リソースフィールド

`resources`フィールドは、エージェントにローカルリソースへのアクセス権を付与します。リソースには、ファイル、スキル、ナレッジベースなどが含まれます。

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

- 特定のパス：`file://README.md` または `skill://my-skill.md`
- グロブパターン：`file://.kiro/**/*.md` または `skill://.kiro/skills/**/SKILL.md`
- 絶対パスまたは相対パス

### ファイルリソース

ファイルリソースは、エージェントの起動時にエージェントのコンテキストに直接読み込まれます。エージェントが常に必要とするコンテンツには、これらを使用してください。

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

ナレッジベースリソースを使用すると、エージェントはインデックス化されたドキュメントやコンテンツを検索できます。数百万トークン規模のインデックス化されたコンテンツと増分読み込みがサポートされているため、エージェントは大規模なドキュメントセットを効率的に検索できます。

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

**フィールド：**

| フィールド | 必須 | 説明 |
| --- | --- | --- |
| `type` | はい | 必須`"knowledgeBase"` |
| `source` | はい | index へのパス。ローカルパスには `file://` というプレフィックスを使用してください |
| `name` | はい | ナレッジベースの表示名 |
| `description` | いいえ | コンテンツの概要 |
| `indexType` | いいえ | インデックス作成戦略：`"best"`（デフォルト、高品質）または `"fast"`（インデックス作成が速い） |
| `autoUpdate` | なし | エージェントの生成時に再インデックス。デフォルト：`false` |

**使用例：**

- エージェント間でチームドキュメントを共有する
- エージェントにプロジェクト固有のコンテキスト（仕様書、決定事項、会議議事録）へのアクセス権を付与する
- 大規模なコードベースやドキュメントをインデックス化する
- `autoUpdate: true` を使用してエージェントの知識を最新の状態に保つ

## フックフィールド

`hooks`フィールドは、CLI V3においてプロファイルスコープのインラインフックを追加します。PascalCase形式のトリガーを含む現在の配列形式を使用してください。特定のエージェントプロファイルに依存せず、またはIDEとCLIをまたいで動作させる必要がある自動化については、代わりに独立した[`.kiro/hooks/*.json`ファイル](https://kiro.dev/docs/hooks/)を使用してください。

json

```json
{
  "hooks": [
    {
      "name": "git-status",
      "trigger": "SessionStart",
      "action": { "type": "command", "command": "git status" }
    },
    {
      "name": "audit-shell",
      "trigger": "PreToolUse",
      "matcher": "^(execute_bash|execute_pwsh)$",
      "action": {
        "type": "command",
        "command": "{ echo \"$(date) - Shell call:\"; cat; echo; } >> /tmp/shell_audit_log"
      }
    },
    {
      "name": "format-writes",
      "trigger": "PostToolUse",
      "matcher": "^(fs_write|fs_append|str_replace|delete_file)$",
      "action": { "type": "command", "command": "cargo fmt --all" }
    }
  ]
}
```

各インラインフックは、スタンドアロンのフックと同じ定義フィールドを使用します：

- `name`（必須）：人間が読み取れる識別子。
- `trigger`（必須）：`SessionStart`、`PreToolUse`、`PostToolUse` などの PascalCase 形式のトリガー名。
- `matcher`（オプション）：ツールセレクタ（ツール ID、`shell`、`@mcp`、`@<server>` などのカテゴリタグ、または `*` ワイルドカード）か、ツール使用トリガー用のツール ID に対して正規表現でマッチさせるもの。[Hook の種類を](https://kiro.dev/docs/hooks/types/#pre-tool-use)参照してください。
- `action`（必須）：コマンドまたはエージェントアクション。「[フックアクション」](https://kiro.dev/docs/hooks/actions/)を参照してください。

CLI 2.x の組み込みフックでは、`agentSpawn`、`preToolUse`、`postToolUse` などのキャメルケースのイベント名をキーとするオブジェクトが使用されていました。CLI V3 では、互換性のためこのレガシー形式を引き続き認識しており、[`/upgrade-agent`](https://kiro.dev/docs/custom-agents/configuration-reference/#upgrading-from-older-configs) によってオブジェクト形式のフックが現在の配列形式に変換されます。

レガシーのCLI 2.xプロファイルでは、`use_aws`マッチャーは構造化された組み込みのAWSツールを対象としており、`aws`はその優先エイリアスでした。このツールはV3の組み込みツールではないため、マッチャーを残しても利用可能にはなりません。移行の際にはAWSツールへの参照を置き換え、変換後のフックでは現在のシェルまたはMCPツール名を使用してください。

## includeMcpJson フィールド

`includeMcpJson`フィールドは、MCP設定ファイルで定義されたMCPサーバー（グローバル用は`~/.kiro/settings/mcp.json`、ワークスペース用は`<cwd>/.kiro/settings/mcp.json`）を含めるかどうかを決定します。

json

```json
{
  "includeMcpJson": true
}
```

`true` に設定すると、エージェントは、エージェントの `mcpServers` フィールドで定義されているものに加え、グローバルおよびローカル設定で定義されているすべての MCP サーバーにアクセスできるようになります。

## Model フィールド

`model`フィールドは、このエージェントで使用するモデルIDを指定します。指定がない場合、エージェントはデフォルトのモデルを使用します。

json

```json
{
  "model": "claude-sonnet-4.6"
}
```

モデル ID は、Kiro のモデルサービスによって返される利用可能なモデルのいずれかと一致している必要があります。利用可能なモデルは、アクティブなチャットセッションで `/model` コマンドを使用することで確認できます。

指定したモデルが利用できない場合、自動的にフォールバックされるのではなく、最初のプロンプトが失敗する可能性があります。Kiro は、エージェントのピンが適用される前にアクティブだったモデルを復元します。復元されたモデルを使用して再試行するか、`/model` を使用して別のモデルを選択するか、エージェントの `model` フィールドを更新してください。

## KeyboardShortcut フィールド

`keyboardShortcut`フィールドはCLI 2.xに属するものであり、CLI V3プロファイルスキーマの一部ではありません。以下の詳細は、レガシープロファイルに関するものです。

json

```json
{
  "keyboardShortcut": "ctrl+a"
}
```

ショートカットは、`+`で区切られた修飾子とキーで構成されます：

**修飾キー**（オプション）：

- `ctrl` - Control キー
- `shift` - Shiftキー

**キー**:

- 1文字: `a-z`（大文字・小文字を区別しない）
- 1桁の数字：`0-9`

**例**:

json

```json
{
  "keyboardShortcut": "ctrl+a"
}
```

json

```json
{
  "keyboardShortcut": "shift+b"
}
```

**動作の切り替え:**

キーボードショートカットを押したとき:

- 別のエージェントを選択している場合：このエージェントに切り替わります
- すでにこのエージェントにいる場合：前のエージェントに戻る

**競合時の処理:**

複数のエージェントで同じキーボードショートカットが設定されている場合、警告が記録され、そのショートカットは無効化されます。この場合は、`/agent swap`を使用して手動で切り替えてください。

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

デフォルトでは、カスタムエージェントは、独自に構成されたリソースに加えて、デフォルトのリソース（ステアリングファイル、スキル、および `AGENTS.md`）を継承します。`chat.disableInheritingDefaultResources` CLI 設定を使用すると、この動作を無効にすることができます。

| プロパティ | 値 |
| --- | --- |
| 設定キー | `chat.disableInheritingDefaultResources` |
| 型 | ブール値 |
| デフォルト | `false`（カスタムエージェントはデフォルトのリソースを継承します） |
| スコープ | グローバル、またはワークスペースで上書き可能 |

CLI を使用して設定します:

bash

```bash
kiro-cli settings chat.disableInheritingDefaultResources true
```

または、ワークスペースにスコープを限定するには:

bash

```bash
kiro-cli settings --workspace chat.disableInheritingDefaultResources true
```

`true`に設定した場合、カスタム（ユーザー定義）エージェントは、コンテキスト内でデフォルトのステアリング、スキル、または`AGENTS.md`を受け取りません。組み込みエージェントは、この設定にかかわらず、常にデフォルトのリソースを継承します。

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
  "permissions": {
    "rules": [
      { "capability": "mcp", "match": ["git/git_status"], "effect": "allow" },
      { "capability": "shell", "match": ["cargo *", "git *"], "effect": "allow" },
      { "capability": "fs_write", "match": ["src/**", "tests/**", "Cargo.toml"], "effect": "allow" },
      { "capability": "shell", "match": ["rm -rf *"], "effect": "deny" }
    ]
  },
  "resources": [
    "file://README.md",
    "file://docs/**/*.md"
  ],
  "hooks": [
    {
      "name": "git-status",
      "trigger": "SessionStart",
      "action": { "type": "command", "command": "git status" }
    },
    {
      "name": "format-writes",
      "trigger": "PostToolUse",
      "matcher": "^(fs_write|fs_append|str_replace|delete_file)$",
      "action": { "type": "command", "command": "cargo fmt --all" }
    }
  ],
  "includeMcpJson": true,
  "model": "claude-sonnet-4.6",
  "welcomeMessage": "Ready to help with AWS and Rust development!"
}
```

## ベストプラクティス

1. **制限的な設定から始める**：ツールへのアクセス権限は最小限から始め、必要に応じて拡大する
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

**グローバルエージェントの使用対象：**

- プロジェクトを横断する汎用エージェント
- 個人の生産性向上用エージェント
- プロジェクト固有のコンテキストを持たないエージェント
- よく使われるツールとワークフロー

### セキュリティのベストプラクティス

- 機能レベルの`permissions.rules`を慎重に確認する
- ワイルドカードよりも具体的なパターンを使用する
- `permissions.rules` を使用して機密性の高い操作を制限する
- まず安全な環境でエージェントをテストする

#### ツールの権限を設定する

`permissions.rules` を使用して、エージェントに必要な書き込み操作のみを事前に承認してください。その他の書き込み操作には実行時の承認が必要であり、一致する `deny` ルールが引き続き適用されます。

書き込みツール（`write`、`shell`、または書き込み機能を持つ MCP ツールなど）を有効にすると、エージェントはユーザーアカウントと同じファイルシステム権限で動作します。つまり、

- エージェントは、`~/.kiro` 以下のすべてのファイル（スキルコンテキストファイル、ステアリングファイル、MCP サーバー設定（`mcp.json`）、およびその他のエージェント設定を含む）を読み取り、変更できます。
- インストールされたすべてのスキルおよびリソースは、同じ権限を共有します。個々のスキル間の分離は存在せず、書き込みツールが有効になっている場合、エージェントはどのスキルのコンテキストでも読み取りや変更を行うことができます。
- スキル自体はコードを実行することはできません。スキルは、エージェントに指示を与えるテキストファイルです。ただし、`shell`のような書き込みツールが許可されている場合、エージェントは読み込まれたスキル内で参照されているコマンドを実行する可能性があります。

書き込みツールを使用する際のリスクを軽減するには：

- 必要な特定の書き込みツールのみを有効にしてください（例：`"write"`は有効にするが、`"shell"`は有効にしない）。
- `permissions.rules` を使用して、`match` パターンを厳格に定義し、パスおよびコマンドの制限を設定してください。
- スキルやリソースをインストールする前に、特に信頼できないソースからのものは、必ず確認してください。
- `PreToolUse`[フック](https://kiro.dev/docs/hooks/)を使用して、機密性の高い操作を監査またはブロックしてください。

## 古い設定からのアップグレード

IDE 1.0 または CLI V3 より前に作成されたエージェント設定がある場合、`/upgrade-agent` は移行が必要な設定を特定し、元の設定をバックアップして、サポートされているフィールドをその場で変換します。

bash

```bash
# Scan agents and open the selection menu
/upgrade-agent

# Review previously upgraded agents and any conversion warnings
/upgrade-agent diagnostics
```

このコマンドは、ワークスペース内の `.kiro/agents/` とグローバルな `~/.kiro/agents/` をスキャンし、スコープごとにグループ化された選択メニューを表示します。アップグレードが必要なエージェントのみが表示されます。

レガシーな CLI 2.x プロファイルは V3 の `/agent` ピッカーに表示される場合がありますが、サポートされていない信頼フィールドを含むプロファイルは、`/upgrade-agent` によって変換されるまで、別のエージェントにフォールバックされます。 `/upgrade-agent`は、オブジェクト形式のフックをHookドキュメントの配列に平坦化します。互換性のためのエイリアスとして、従来のcamelCase形式のトリガー文字列は保持されます。移行されたフックを手動で作成または修正する際は、PascalCaseを使用してください。

以前：

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

`/upgrade-agent` 後の状態:

json

```json
{
  "hooks": [
    {
      "name": "agentSpawn-0",
      "trigger": "agentSpawn",
      "action": {
        "type": "command",
        "command": "git status"
      },
      "timeout": 10
    }
  ]
}
```

Kiro V3は、生成された`agentSpawn`というエイリアスを受け入れます。新規設定の場合は、これを`SessionStart`にリネームし、生成された名前をHookの目的を説明する名前に置き換えてください。

### 変換される内容

| 従来のパターン | 新しい対応形 |
| --- | --- |
| `toolsSettings.shell.allowedCommands` | `permissions.rules` → `capability: shell`、`effect: allow` |
| `toolsSettings.shell.deniedCommands` | `permissions.rules` → `capability: shell`、`effect: deny` |
| `toolsSettings.write.allowedPaths` | `permissions.rules` を `capability: fs_write`、`effect: allow` に変換 |
| `allowedTools`のエントリ | 機能レベルの許可ルール |
| `toolsSettings.subagent.trustedAgents` | `permissions.rules` と `capability: subagent`、`effect: allow`。サブエージェントの起動に関するプロンプトのみをスキップします |
| `toolsSettings.subagent.availableAgents` | `subagent/<name>` 内の <img id="1"/> エントリを `tools` と <img id="3"/> で置き換えます。`subagent`。`tools` に `@builtin` または `*` も含まれている場合、生成を制限しません。 |
| CLI 2.x ツール名（`fs_read`、`execute_bash`） | `/upgrade-agent` によって選択された現在のセレクタ (`read`、`shell`) |
| `autoAllowReadonly` | 読み取り専用シェルポリシールール |
| オブジェクト形式のフック | Hookドキュメントのフラットな配列。レガシーなトリガーエイリアスは引き続き受け入れられる |

元のファイルは `<filename>.json.bak` にバックアップされます。元に戻すには、バックアップファイルの名前を元に戻してください。

診断やトラブルシューティングを含む完全なアップグレードガイドについては、[「CLI V3の新機能 - エージェント設定」を](https://kiro.dev/docs/cli/v3/agent-config/)参照してください。

## 次の手順

- [カスタムエージェントの作成](https://kiro.dev/docs/custom-agents/creating/)
- [例](https://kiro.dev/docs/custom-agents/examples/) - 実際のエージェント設定
- [トラブルシューティング](https://kiro.dev/docs/custom-agents/troubleshooting/) - 一般的な問題の解決
- [フックのドキュメント](https://kiro.dev/docs/hooks/)


---

[← 前へ: Creating custom agents](creating.md) | [↑ 親ページ](index.md) | [次へ →: Invoking as sub-agents](subagents.md)
