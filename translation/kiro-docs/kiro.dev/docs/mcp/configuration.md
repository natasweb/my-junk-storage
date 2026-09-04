# Configuration

> 元URL: https://kiro.dev/docs/mcp/configuration/  
> 最終取り込み日: 2026-09-05  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

このガイドでは、Kiro を使用した Model Context Protocol (MCP) サーバーの設定に関する詳細な情報を提供します。これには、設定ファイルの構造、サーバーのセットアップ、およびすべての管理インターフェースにわたる管理方法などが含まれます。

## 設定ファイルの構造

MCP設定ファイルはJSON形式を採用しており、以下の構造となっています：

json

```json
{
  "mcpServers": {
    "local-server-name": {
      "command": "command-to-run-server",
      "args": ["arg1", "arg2"],
      "env": {
        "ENV_VAR1": "hard-coded-variable",
        "ENV_VAR2": "${EXPANDED_VARIABLE}"
      },
      "disabled": false,
      "autoApprove": ["tool_name1", "tool_name2"],
      "disabledTools": ["tool_name3"]
    },
    "remote-server-name": {
      "url": "https://endpoint.to.connect.to",
      "headers": {
        "HEADER1": "value1",
        "HEADER2": "value2"
      },
      "disabled": false,
      "autoApprove": ["tool_name1", "tool_name2"],
      "disabledTools": ["tool_name3"]
    }
  }
}
```

### 設定プロパティ

#### ローカルサーバー

| プロパティ | 型 | 必須 | 説明 |
| --- | --- | --- | --- |
| `command` | 文字列 | はい | MCP サーバーを実行するためのコマンド |
| `args` | 配列 | いいえ | コマンドに渡す引数 |
| `env` | オブジェクト | なし | サーバープロセス用の環境変数 |
| `disabled` | ブール値 | いいえ | サーバーが無効化されているかどうか（デフォルト：false） |
| `autoApprove` | 配列 | いいえ | 確認メッセージを表示せずに自動承認するツール名（すべてのツールを自動承認するには ``"*"`` を使用してください） |
| `disabledTools` | 配列 | いいえ | エージェントを呼び出す際に除外するツール名 |

#### リモートサーバー

| プロパティ | タイプ | 必須 | 説明 |
| --- | --- | --- | --- |
| `url` | 文字列 | はい | リモート MCP サーバーの HTTPS エンドポイント（またはローカルホストの場合は HTTP エンドポイント） |
| `headers` | オブジェクト | いいえ | 接続時にMCPサーバーに渡すヘッダー |
| `env` | オブジェクト | なし | サーバープロセス用の環境変数 |
| `oauth` | オブジェクト | なし | 認証が必要なサーバーの OAuth 設定（[OAuth 設定を](https://kiro.dev/docs/mcp/configuration/#oauth-configuration)参照） |
| `oauthScopes` | 配列 | なし | リクエストする OAuth スコープ（フォールバック。両方が設定されている場合は、`oauth.oauthScopes` によって上書きされます） |
| `disabled` | ブール値 | いいえ | サーバーが無効化されているかどうか（デフォルト：false） |
| `autoApprove` | 配列 | いいえ | 確認メッセージを表示せずに自動承認するツール名（すべてのツールを自動承認するには ``"*"`` を使用してください） |
| `disabledTools` | 配列 | いいえ | エージェントを呼び出す際に除外するツール名 |

## 設定場所

MCPサーバーは、次の2つのレベルで設定できます：

1. **ワークスペースレベル**：`.kiro/settings/mcp.json`

   - 現在のワークスペースにのみ適用されます
   - プロジェクト固有のMCPサーバーに最適
2. **ユーザーレベル**：`~/.kiro/settings/mcp.json`

   - すべてのワークスペースにグローバルに適用されます
   - 頻繁に使用する MCP サーバーに最適

両方のファイルが存在する場合、設定はマージされ、ワークスペースの設定が優先されます。

### 設定ファイルの作成

**コマンドパレットの使用方法:**

1. コマンドパレットを開きます（Macの場合は`Cmd + Shift + P`、Windows/Linuxの場合は`Ctrl + Shift + P`）
2. 「MCP」を検索し、以下のオプションのいずれかを選択します：
   - **Kiro: ワークスペースの MCP 設定 (JSON) を開く** - ワークスペースレベルの設定用
   - **Kiro: ユーザー用 MCP 設定ファイル (JSON) を開く** - ユーザーレベルの設定用

**Kiro パネルの使用方法:**

1. Kiroパネルを開きます
2. **「MCP設定を開く」**アイコンを選択

### MCP サポートの有効化

1. `Cmd + ,`（Mac）または `Ctrl + ,`（Windows/Linux）で「設定」を開く
2. 「MCP」を検索します
3. MCPサポートの設定を有効にする

### 変更の適用

MCP 設定の変更は、ファイルを保存すると自動的に反映されます。設定ファイル（`Cmd+S`）を保存すると、サーバーが再接続されます。

## MCP サーバーの読み込み優先順位

複数の設定で同じMCPサーバーが指定されている場合、以下の優先順位（高い順から低い順）に基づいて読み込まれます：

1. **エージェント設定** - エージェント JSON 内の `mcpServers` フィールド
2. **ワークスペースのMCP JSON** - `.kiro/settings/mcp.json`
3. **グローバル MCP JSON** - `~/.kiro/settings/mcp.json`

### 使用例

**完全な上書き：**

```
Agent config:     { "fetch": { command: "fetch-v2" } }
Workspace config: { "fetch": { command: "fetch-v1" } }
Global config:    { "fetch": { command: "fetch-old" } }

Result: Only "fetch-v2" from agent config is used
```

**追加設定（異なる名前）：**

```
Agent config:     { "fetch": {...} }
Workspace config: { "git": {...} }
Global config:    { "aws": {...} }

Result: All three servers are used (fetch, git, aws)
```

**オーバーライドによる無効化:**

```
Agent config:     { "fetch": { command: "...", disabled: true } }
Workspace config: { "fetch": { command: "..." } }

Result: No fetch server is launched
```

## 設定例

### 環境変数を使用したローカルサーバー

json

```json
{
  "mcpServers": {
    "web-search": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-bravesearch"
      ],
      "env": {
        "BRAVE_API_KEY": "${BRAVE_API_KEY}"
      }
    }
  }
}
```

### ヘッダーを使用したリモートサーバー

json

```json
{
  "mcpServers": {
    "api-server": {
      "url": "https://api.example.com/mcp",
      "headers": {
        "Authorization": "Bearer ${API_TOKEN}",
        "X-Custom-Header": "value"
      }
    }
  }
}
```

### 複数のサーバー

json

```json
{
  "mcpServers": {
    "fetch": {
      "command": "uvx",
      "args": ["mcp-server-fetch"]
    },
    "git": {
      "command": "uvx",
      "args": ["mcp-server-git"],
      "env": {
        "GIT_CONFIG_GLOBAL": "/dev/null"
      }
    },
    "aws-docs": {
      "command": "npx",
      "args": ["-y", "@aws/aws-documentation-mcp-server"]
    }
  }
}
```

## 環境変数

多くのMCPサーバーでは、認証や設定のために環境変数が必要です。環境変数を参照するには、`${VARIABLE_NAME}`という構文を使用します。

json

```json
{
  "mcpServers": {
    "server-name": {
      "env": {
        "API_KEY": "${YOUR_API_KEY}",
        "DEBUG": "true",
        "TIMEOUT": "30000"
      }
    }
  }
}
```

セキュリティ上の理由から、Kiroは明示的に承認された環境変数のみを展開します。承認されていない環境変数を含むMCPサーバーの設定を追加または変更すると、Kiroは承認が必要な変数を一覧表示したセキュリティ警告ポップアップを表示します。

承認済みの環境変数を管理するには：

1. Kiroの設定を開く
2. 「**Mcp Approved Env Vars**」を検索します
3. 展開を許可したい環境変数を追加します

## OAuth認証

OAuth 認証を必要とするリモート MCP サーバーがサポートされています。Kiro は、OAuth で保護されたサーバーに接続する際、ブラウザベースの OAuth フローを自動的に処理します。

json

```json
{
  "mcpServers": {
    "remote-server-with-oauth": {
      "url": "https://api.example.com/mcp",
      "oauth": {
        "clientId": "your-client-id",
        "redirectUri": "http://127.0.0.1:8080/oauth/callback",
        "oauthScopes": ["read", "write"]
      }
    }
  }
}
```

OAuthのスコープに関するエラーが発生した場合は、空の配列を使用してください：`"oauthScopes": []`

ほとんどのサーバーは[ダイナミック・クライアント登録](https://datatracker.ietf.org/doc/html/rfc7591) (DCR) を使用しており、追加の設定は必要ありません。接続するだけで、Kiro が認証ページを開きます。

### OAuthの設定

Figma、Slack、GitHub など、動的クライアント登録 (DCR) をサポートしていないサーバーの場合は、`oauth` オブジェクトに独自の OAuth 認証情報を指定できます。これは、**Cognito**、**Auth0**、**Okta** などの認証サーバーで機能します。

json

```json
{
  "mcpServers": {
    "figma": {
      "url": "https://mcp.figma.com/mcp",
      "oauth": {
        "clientId": "my-figma-client-id",
        "clientSecret": "my-figma-client-secret",
        "redirectUri": "http://localhost:7778/oauth/callback",
        "oauthScopes": ["files:read"]
      }
    }
  }
}
```

この設定を使用するには、Figma Developer ConsoleでOAuthアプリを登録してください。アプリ設定のリダイレクトURIを`http://localhost:7778/oauth/callback`に設定してください。ポートとパスは完全に一致している必要があります。Figmaでは機密クライアントが必須であるため、`clientId`と`clientSecret`の両方が必要です。

#### OAuth プロパティ

| プロパティ | タイプ | 必須 | 説明 |
| --- | --- | --- | --- |
| `oauth.clientId` | 文字列 | なし | 事前登録済みの OAuth クライアント ID。設定すると、動的クライアント登録は完全にスキップされます。 |
| `oauth.clientSecret` | 文字列 | No | クライアントシークレットを必要とするサーバー用のクライアントシークレット。`clientId` と併用した場合にのみ有効です。 |
| `oauth.redirectUri` | 文字列 | なし | OAuthコールバック用のカスタムループバックリダイレクトURI。以下の[リダイレクトURIの形式を](https://kiro.dev/docs/mcp/configuration/#redirect-uri-formats)参照してください。 |
| `oauth.clientMetadataUrl` | 文字列 | なし | ホストされているクライアント ID メタデータ ドキュメントの HTTPS URL。設定すると、Kiro はその URL として認証を行い、クライアントを登録しません。以下の「[クライアント ID メタデータ ドキュメント」](https://kiro.dev/docs/mcp/configuration/#client-id-metadata-documents-cimd)を参照してください。 |
| `oauth.oauthScopes` | 配列 | なし | 認証サーバーにリクエストする OAuth スコープ。最上位の ``oauthScopes`` よりも優先されます。 |

#### 動作の仕組み

- **`clientId` または `clientMetadataUrl` が設定されていない場合** - Kiro はサーバーに対して動的クライアント登録（DCR）を試行します。DCR が失敗した場合は、デフォルトのクライアント設定にフォールバックします。
- **`clientId` 設定済み（`clientSecret` なし）** - Kiro は DCR をスキップし、登録済みのクライアント ID を使用してパブリック OAuth クライアントとして認証を行います。
- **`clientId` および `clientSecret` の両方が設定されている場合（CLIのみ）** - KiroはDCRをスキップし、機密クライアントとして認証を行い、トークンエンドポイントにシークレットを送信します。これは、Figmaのように機密クライアントにのみトークンを発行するサーバーで必要です。
- **`clientMetadataUrl` 設定済み（`clientId` なし）** - Kiro は DCR をスキップし、認証サーバーがサポートしている場合、メタデータドキュメントの URL をクライアント ID として提示します。以下の「[クライアント ID メタデータドキュメント」](https://kiro.dev/docs/mcp/configuration/#client-id-metadata-documents-cimd)を参照してください。

独自のIDプロバイダーを使用する場合、MCPサーバーは、そのプロバイダーのエンドポイントを指す`/.well-known/oauth-authorization-server`メタデータドキュメントを提供し、プロバイダーのJWKSに対してベアラートークンの検証を行う必要があります。

#### リダイレクト URI の形式

`redirectUri` フィールドは、いくつかの形式を受け入れます。ホストは `127.0.0.1` または `localhost` でなければならず、スキーマは `http` でなければなりません（コールバックはローカルのループバックサーバーによって提供されます）。

| 形式 | 例 | 説明 |
| --- | --- | --- |
| 完全なURL | `http://localhost:7778/oauth/callback` | 事前に登録されたアプリに一致するように、ポートとパスを固定する |
| ホストとポート | `127.0.0.1:7778` | ポートを固定します。パスはデフォルトで `/` になります。 |
| ポートのみ | `:7778` | `127.0.0.1` のポートを固定 |
| 省略 | *（未設定）* | OS が利用可能なポートをランダムに割り当てます |

OAuth アプリに、特定のコールバックパスを含む事前登録済みのリダイレクト URI が設定されている場合は、カスタムパスを含む完全な URL を使用してください。

#### クライアント ID メタデータドキュメント (CIMD)

[クライアント ID メタデータドキュメント](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-client-id-metadata-document)をホストしている場合は、`oauth.clientMetadataUrl` をその HTTPS URL に設定してください。DCR を通じてクライアントを登録する代わりに、Kiro はその URL をクライアント ID として提示し、認証サーバーがそのドキュメントを取得してクライアントのメタデータを取得します。これは、認証サーバーが `client_id_metadata_document_supported` を公開している場合にのみ機能します。そうでない場合、Kiro は DCR にフォールバックします。

json

```json
{
  "mcpServers": {
    "enterprise-server": {
      "url": "https://mcp.example.com/mcp",
      "oauth": {
        "clientMetadataUrl": "https://apps.example.com/kiro-client.json",
        "redirectUri": "http://127.0.0.1:8080/oauth/callback"
      }
    }
  }
}
```

`clientMetadataUrl` と `clientId` の両方を設定しないでください。`clientId` が存在する場合、Kiro はそれを使用し、`clientMetadataUrl` には一切アクセスしません。

#### スコープ

OAuthスコープは、次の2か所で指定できます：

json

```json
{
  "mcpServers": {
    "server": {
      "url": "https://mcp.example.com",
      "oauthScopes": ["openid", "email"],
      "oauth": {
        "clientId": "my-id",
        "oauthScopes": ["read:data", "write:data"]
      }
    }
  }
}
```

両方が設定されている場合、`oauth.oauthScopes` が優先されます。どちらも指定されていない場合、Kiro はデフォルトのスコープセット（`openid`、`email`、`profile`、`offline_access`）をリクエストします。

### セッション中のトークン更新

セッション中に OAuth トークンの有効期限が切れており、リフレッシュトークンが利用できない場合、Kiro は自動的に新しいブラウザベースの認証フローを開始します。 セッションを再起動する必要はありません。再認証は透過的に行われ、MCP サーバーは新しいトークンで再接続します。IDE では、トークンが期限切れになると、MCP パネルに警告インジケーターと [**再認証]** ボタンが表示されます。

これは、リフレッシュトークンなしで有効期間の短いトークンを発行する ID プロバイダの場合に特に便利です。

### 認証情報の管理（CLI）

自動更新では不十分な場合（たとえば、トークンが失効したときやアカウントを切り替える必要があるときなど）、CLI で OAuth 認証情報を手動で管理できます:

| コマンド | キーボードショートカット | 説明 |
| --- | --- | --- |
| `/mcp auth` | `^A` | トークンの有効期限が切れた場合や無効な場合に、再認証を強制する |
| `/mcp cancel-auth` | `^X` | ブラウザの確認待ちで停止している認証フローを中止する |
| `/mcp logout` | `^R` | サーバーの保存済み認証情報を削除する |

MCPパネルのステータスビューでは、キーボードショートカットが利用可能です。詳細な使用方法については、「[スラッシュコマンド」](https://kiro.dev/docs/reference/slash-commands/#mcp-auth)を参照してください。

## ホットリロード

ディスクに変更を保存すると、エージェントおよびMCPの設定がホットリロードされます。ファイルウォッチャーが`.kiro/agents`ディレクトリおよび`mcp.json`ファイルを監視し、セッションを再起動したり会話のコンテキストを失ったりすることなく、実行中のサーバーとエージェントの状態を同期させます。

これは以下に適用されます：

- 既存のエージェント設定ファイルまたは`mcp.json`の編集
- エージェントファイルの追加または削除
- MCPサーバーエントリの追加、削除、または編集

同期の仕組み：

- **変更されたサーバーのみが再起動されます**。サーバーエントリの追加、削除、編集を行った場合、影響を受けるサーバーのみが停止または起動されます。変更されていないサーバーは引き続き稼働します。
- **順序に依存しない設定の差分** — 環境変数やJSONキーの順序を変更しても、変更とはみなされず、再起動はトリガーされません。
- **セッション中に追加されたサーバーは保持されます** - `/mcp add` 経由でセッション中に追加されたサーバーは、リコンサイレメント中に再マージされます。

リロードをトリガーするためのコマンドは不要です。ファイルを保存すると、次のアイドル境界（ターン間）で変更が反映されます。

## サーバーおよびツールの無効化

設定を削除せずにMCPサーバーを一時的に無効にするには、`disabled`を`true`に設定します：

json

```json
{
  "mcpServers": {
    "server-name": {
      "disabled": true
    }
  }
}
```

サーバーをアクティブなままにしながら、エージェントが特定のツールを使用できないようにするには、`disabledTools` を使用します:

json

```json
{
  "mcpServers": {
    "server-name": {
      "disabledTools": ["delete_file", "execute_command"]
    }
  }
}
```

## MCPレジストリ（エンタープライズ）

IAM Identity Center を使用しているエンタープライズチームの場合、MCP レジストリを通じて MCP サーバーへのアクセスを一元的に制御できます。詳細については、[MCP レジストリ](https://kiro.dev/docs/mcp/registry/)のページを参照してください。

## 設定のトラブルシューティング

1. **JSON構文の検証**

   - JSONに構文エラーがなく、有効であることを確認してください
   - コンマ、引用符、括弧の抜けがないか確認してください
   - JSONバリデータまたはリンターを使用してください
2. **コマンドのパスを確認する**

   - 指定したコマンドが PATH に存在することを確認してください
   - ターミナルで直接コマンドを実行してみてください
3. **環境変数を確認してください**

   - 必要な環境変数がすべて設定されていることを確認してください
   - 環境変数名にタイプミスがないか確認してください
4. **設定ファイルの読み込み状況を確認してください**

   - どの設定ファイルが読み込まれているか、およびそれらの優先順位を確認してください:

   bash

   ```bash
   # Check workspace config
   cat .kiro/settings/mcp.json

   # Check user config
   cat ~/.kiro/settings/mcp.json
   ```

## セキュリティ上の考慮事項

MCP サーバーを設定する際は、以下のセキュリティに関するベストプラクティスに従ってください:

- 機密値をハードコーディングするのではなく、環境変数の参照（例: `${API_TOKEN}`）を使用してください
- 認証情報を含む設定ファイルをバージョン管理システムにコミットしないでください
- 信頼できるリモートサーバーにのみ接続してください
- `autoApprove` にツールを追加する前に、その権限を確認してください
- `disabledTools` を使用して、危険な操作へのアクセスを制限してください

包括的なセキュリティガイダンスについては、[MCP セキュリティのベスト](https://kiro.dev/docs/mcp/security/)プラクティスのページを参照してください。


---

[← 前へ: MCP](index.md) | [↑ 親ページ](index.md) | [次へ →: Server directory](servers.md)
