# Examples

> 元URL: https://kiro.dev/docs/mcp/examples/  
> 最終取り込み日: 2026-09-05  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

このガイドでは、Model Context Protocol（MCP）サーバーのいくつかの例、その機能、およびKiroでの設定方法について説明します。

## AWSドキュメントサーバー

AWSドキュメントサーバーは、AWSドキュメントへのアクセス、検索機能、およびコンテンツのおすすめ機能を提供します。
機能

- すべてのサービスにわたるAWSドキュメントの検索
- Markdown形式でドキュメントページを閲覧
- 特定のドキュメントページに関連するコンテンツのおすすめを取得

### セットアップ手順

#### 前提条件

1. Astral から uv をインストールします:

bash

```bash
# On macOS/Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# On Windows PowerShell
irm https://astral.sh/uv/install.ps1 | iex
```

2. Python 3.10 以降をインストールします:

bash

```bash
    uv python install 3.10
```

#### 設定

macOS/Linuxの場合:

json

```json
{
  "mcpServers": {
    "aws-docs": {
      "command": "uvx",
      "args": ["awslabs.aws-documentation-mcp-server@latest"],
      "env": {
        "FASTMCP_LOG_LEVEL": "ERROR"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

Windowsの場合:

json

```json
{
  "mcpServers": {
    "aws-docs": {
      "command": "uv",
      "args": [
        "tool",
        "run",
        "--from",
        "awslabs.aws-documentation-mcp-server@latest",
        "awslabs.aws-documentation-mcp-server.exe"
      ],
      "env": {
        "FASTMCP_LOG_LEVEL": "ERROR"
      }
    }
  }
}
```

### 利用可能なツール

| ツール名 | 説明 |
| --- | --- |
| mcp_aws_docs_search_documentation | 特定のトピックについてAWSドキュメントを検索する |
| mcp_aws_docs_read_documentation | AWSドキュメントページをMarkdown形式で閲覧する |
| mcp_aws_docs_recommend | ドキュメントページに関連するコンテンツのおすすめを取得する |

### 使用例

```
# Search for information about S3 bucket policies
Search AWS documentation for S3 bucket policies

# Read specific documentation
Read the AWS Lambda function URLs documentation

# Get recommendations
Find related content to AWS ECS task definitions
```

## GitHub MCP サーバー

GitHub MCP サーバーにより、Kiro は GitHub のリポジトリ、イシュー、プルリクエストとやり取りできるようになります。

### 機能

- ファイル、コミット、ブランチなどのリポジトリ情報にアクセスする
- イシューやプルリクエストの作成および管理
- リポジトリ内で特定のコンテンツを検索する

### セットアップ手順

### 前提条件

1. まだインストールされていない場合は、Docker をインストールしてください：

   - macOSおよびWindows用のDocker Desktop
   - Linux用 Docker Engine
2. GitHubの個人用アクセストークンを作成します：

   - GitHub の [設定] > [開発者設定] > [個人用アクセストークン (詳細設定)] に移動します
   - 必要なツールに適した権限を持つ新しいトークンを生成します

### 設定

GitHubの公式ドキュメントに記載されている以下の手順に従ってください:

1. ワークスペースディレクトリに ``.kiro/settings/mcp.json`` ファイルを作成します（すでに存在する場合は編集します）
2. 以下の設定を追加します：

json

```json
{
  "mcpServers": {
    "github": {
      "command": "docker",
      "args": [
        "run", 
        "-i", 
        "--rm", 
        "-e", 
        "GITHUB_PERSONAL_ACCESS_TOKEN",
        "ghcr.io/github/github-mcp-server"
      ],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "your-token-here"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

より詳細なインストール手順については、GitHub MCP Serverの公式ドキュメントを参照してください。

### 一般的なツール

GitHub MCP サーバーは、GitHub と連携するための包括的なツールセットを提供しています。以下に、カテゴリー別に分類した、最もよく使用されるツールをいくつか紹介します:

| カテゴリ | ツール名 | 説明 |
| --- | --- | --- |
| リポジトリツール | search_repositories | GitHubリポジトリを検索する |
| リポジトリツール | list_branches | リポジトリ内のブランチを一覧表示する |
| イシューツール | list_issues | リポジトリ内のイシューを一覧表示する |
| イシューツール | update_issue | 既存のイシューを更新する |
| Issue Tools | add_issue_comment | イシューにコメントを追加する |
| プルリクエストツール | create_pull_request | 新しいプルリクエストを作成する |

### 利用可能なツールセット

GitHub MCP サーバーは、その機能をツールセットに整理しており、必要に応じて有効化または無効化できます。デフォルトでは、すべてのツールセットが有効になっています。GitHub MCP サーバーの設定時に、有効にするツールセットを指定できます。これにより、AI ツールで利用可能な GitHub API 機能を制御できます。

GitHub MCP サーバーの設定時に、有効にするツールセットを指定できます。これにより、AI ツールで利用可能な GitHub API 機能を制御できます。
Docker でのツールセットの使用

Docker を使用する場合、ツールセットを環境変数として指定できます。

bash

```bash
docker run -i --rm \
  -e GITHUB_PERSONAL_ACCESS_TOKEN=<your-token> \
  -e GITHUB_TOOLSETS="repos,issues,pull_requests,actions,code_security,experiments" \
  ghcr.io/github/github-mcp-server
```

### 使用例

```
# Get repository information
Show me information about the tensorflow/tensorflow repository

# Search for code
Find examples of React hooks in facebook/react

# Create an issue
Create an issue in my repository about the login bug
```

## カスタム MCP サーバー

特定のニーズに合わせて Kiro の機能を拡張するために、独自の MCP サーバーを作成できます。

### カスタムサーバーの作成

1. プログラミング言語（Python、Node.jsなど）を選択します
2. 利用可能なライブラリを使用してMCPプロトコルを実装します
3. ツールとその機能を定義する
4. サーバーをパッケージ化して配布する

#### カスタムサーバー開発のためのリソース

- [MCPプロトコル仕様](https://modelcontextprotocol.io/specification/2025-06-18)
- [MCP サーバーテンプレート（Python）](https://kiro.dev/docs/mcp/examples/)
- [MCP サーバーテンプレート (Node.js)](https://kiro.dev/docs/mcp/examples/)

### その他の MCP サーバー

#### データベースサーバー

- **PostgreSQL MCP サーバー**：PostgreSQL データベースのクエリおよび管理
- **MongoDB MCP サーバー**：MongoDB データベースとの連携

#### 開発ツール

- **Docker MCP サーバー**：Docker コンテナおよびイメージの管理
- **Kubernetes MCP サーバー**：Kubernetes クラスターとの連携

### その他のMCPサーバーを探す

その他のMCPサーバーを探すには：

- [MCPレジストリ](https://github.com/modelcontextprotocol/registry)にアクセスする
- [GitHubのMCP組織](https://github.com/modelcontextprotocol)を確認する
- npm または PyPI で「**mcp-server」**を検索する

## その間

包括的なサンプルを用意するまでの間、以下のことを行ってください:

- 安全な統合のための[セキュリティのベストプラクティスを](https://kiro.dev/docs/mcp/security/)確認する
- [MCPの公式ドキュメント](https://modelcontextprotocol.io/introduction)をご覧ください
- [MCP](https://kiro.dev/docs/mcp/)の[概要](https://kiro.dev/docs/mcp/)に戻る

## 次の手順

- [セキュリティのベストプラクティスを](https://kiro.dev/docs/mcp/security/)確認する
- [MCP](https://kiro.dev/docs/mcp/)の[概要](https://kiro.dev/docs/mcp/)に戻る


---

[← 前へ: Tool search](tool-search.md) | [↑ 親ページ](index.md) | [次へ →: Best practices](security.md)
