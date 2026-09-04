# Agent examples

> 元URL: https://kiro.dev/docs/custom-agents/examples/  
> 最終取り込み日: 2026-09-05  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

このページでは、独自のワークフローを作成する際の出発点として活用できる、カスタムエージェントの実用的な例を紹介しています。

## AWS スペシャリスト・エージェント

このカスタムエージェントは、AWSインフラストラクチャの管理に最適化されています。安全な操作を事前に承認しつつ、破壊的なコマンドを制限します。

json

```json
{
  "name": "aws-specialist-agent",
  "description": "Specialized agent for AWS infrastructure and development tasks",
  "prompt": "You are an expert AWS infrastructure specialist with deep knowledge of cloud architecture and best practices",
  "tools": ["read", "write", "shell", "@git"],
  "permissions": {
    "rules": [
      { "capability": "shell", "match": ["aws *", "terraform *", "git *"], "effect": "allow" },
      { "capability": "fs_write", "match": ["infrastructure/**", "scripts/**", "*.yaml", "*.yml"], "effect": "allow" },
      { "capability": "shell", "match": ["rm -rf *", "sudo *"], "effect": "deny" }
    ]
  },
  "resources": [
    "file://README.md",
    "file://infrastructure/**/*.yaml",
    "file://docs/aws-setup.md"
  ],
  "hooks": [
    {
      "name": "check-aws-identity",
      "trigger": "SessionStart",
      "action": { "type": "command", "command": "aws sts get-caller-identity" }
    }
  ],
  "model": "claude-sonnet-4"
}
```

**ユースケース：**

- CloudFormationスタックのデプロイ
- S3バケットおよびLambda関数の管理
- AWSサービスのトラブルシューティング
- インフラストラクチャ・アズ・コードの確認と更新

## 開発ワークフローエージェント

このカスタムエージェントは、コードレビュー、テスト、Git操作など、一般的なソフトウェア開発タスク向けに設計されています。

json

```json
{
  "name": "development-workflow-agent",
  "description": "General development workflow agent with Git integration",
  "prompt": "You are a software development assistant with expertise in Git workflows and code management",
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/",
      "headers": { "Authorization": "Bearer ${GITHUB_PAT}" },
      "disabledTools": ["create_issue", "update_issue", "create_pull_request", "merge_pull_request", "push_files", "delete_file"]
    }
  },
  "tools": ["read", "write", "shell", "@github/issue_read", "@github/search_issues"],
  "allowedTools": ["read", "@github/issue_read", "@github/search_issues"],
  "toolAliases": {
    "@github/issue_read": "issue",
    "@github/search_issues": "issues"
  },
  "permissions": {
    "rules": [
      { "capability": "fs_write", "match": ["src/**", "tests/**", "docs/**", "*.md", "*.json"], "effect": "allow" },
      { "capability": "shell", "match": ["git *", "npm *"], "effect": "allow" },
      { "capability": "mcp", "match": ["@github/issue_read", "@github/search_issues"], "effect": "allow" }
    ]
  },
  "resources": [
    "file://README.md",
    "file://CONTRIBUTING.md",
    "file://docs/**/*.md"
  ],
  "hooks": [
    {
      "name": "git-status",
      "trigger": "SessionStart",
      "action": { "type": "command", "command": "git status --porcelain" }
    },
    {
      "name": "current-branch",
      "trigger": "SessionStart",
      "action": { "type": "command", "command": "git branch --show-current" }
    }
  ]
}
```

**ユースケース：**

- コードレビューと分析
- テストの作成および更新
- Gitワークフローの管理
- ドキュメントの更新

## コードレビュー用エージェント

このカスタムエージェントは、コードレビュータスクに特化しており、コードの品質、セキュリティ、およびベストプラクティスの分析に最適化されたツールとコンテキストを備えています。

json

```json
{
  "name": "code-review-agent",
  "description": "Specialized agent for code review and quality analysis",
  "prompt": "You are a code review specialist focused on quality, security, and best practices",
  "tools": ["read", "shell"],
  "allowedTools": ["read"],
  "permissions": {
    "rules": [
      { "capability": "shell", "match": ["grep *", "find *", "git diff *", "git log *", "eslint *", "pylint *"], "effect": "allow" },
      { "capability": "shell", "match": ["rm *", "sudo *"], "effect": "deny" }
    ]
  },
  "resources": [
    "file://CONTRIBUTING.md",
    "file://docs/coding-standards.md",
    "file://.eslintrc.json",
    "file://pyproject.toml"
  ],
  "hooks": [
    {
      "name": "changed-files",
      "trigger": "SessionStart",
      "action": { "type": "command", "command": "git diff --name-only HEAD~1" }
    }
  ]
}
```

**ユースケース：**

- コード品質に関するプルリクエストのレビュー
- セキュリティ上の脆弱性の特定
- コーディング標準への準拠確認
- 改善案やリファクタリングの提案

## プロジェクト固有のエージェント

この例では、プロジェクト固有のMCPサーバー、ドキュメント、ビルドプロセスを含め、特定のプロジェクトに合わせてカスタマイズされたエージェントを作成する方法を示します。

json

```json
{
  "name": "mobile-app-agent",
  "description": "Agent for the mobile app backend project",
  "prompt": "You are a backend development specialist for mobile applications with expertise in Docker and database management",
  "mcpServers": {
    "docker": {
      "command": "docker-mcp-server",
      "args": ["--socket", "/var/run/docker.sock"]
    },
    "database": {
      "command": "postgres-mcp-server",
      "args": ["--connection", "postgresql://localhost:5432/myapp"],
      "env": { "PGPASSWORD": "${DATABASE_PASSWORD}" }
    }
  },
  "tools": ["read", "write", "shell", "@docker", "@database"],
  "allowedTools": ["read", "@docker/docker_ps", "@docker/docker_logs", "@database/query_read_only"],
  "toolAliases": {
    "@docker/docker_ps": "containers",
    "@docker/docker_logs": "logs",
    "@database/query_read_only": "query"
  },
  "permissions": {
    "rules": [
      { "capability": "fs_write", "match": ["src/**", "tests/**", "migrations/**", "docker-compose.yml", "Dockerfile"], "effect": "allow" },
      { "capability": "shell", "match": ["npm test", "npm run build", "docker-compose *"], "effect": "allow" },
      { "capability": "shell", "match": ["rm -rf *", "sudo *"], "effect": "deny" }
    ]
  },
  "resources": [
    "file://README.md",
    "file://docs/api-documentation.md",
    "file://docs/database-schema.md",
    "file://docker-compose.yml"
  ],
  "hooks": [
    {
      "name": "compose-status",
      "trigger": "SessionStart",
      "action": { "type": "command", "command": "docker-compose ps" }
    },
    {
      "name": "git-status",
      "trigger": "SessionStart",
      "action": { "type": "command", "command": "git status --porcelain" }
    }
  ]
}
```

**ユースケース：**

- Dockerコンテナおよびサービスの管理
- データベースクエリおよびマイグレーションの実行
- アプリケーションのビルドとテスト
- 本番環境の問題のデバッグ

## リモートMCPサーバーとの統合

この例は、リモートMCPサーバーを使用するように設定されたエージェントを示しています：

json

```json
{
  "name": "domain-finder",
  "description": "Agent with access to domain search capabilities",
  "prompt": "You help users find and research domain names using the find-a-domain service.",
  "mcpServers": {
    "find-a-domain": {
      "type": "http",
      "url": "https://api.findadomain.dev/mcp"
    }
  },
  "tools": ["@find-a-domain"],
  "allowedTools": ["@find-a-domain"]
}
```

このエージェントは、リモートMCPサーバーを介してドメイン検索ツールへのアクセスを提供します。サーバーでOAuth認証が必要な場合は、プロンプトが表示された際に`/mcp`コマンドを使用して認証を行ってください。

### OAuth設定の場合

特定のスコープでのOAuthを必要とするサーバーの場合：

json

```json
{
  "name": "github-agent",
  "description": "Agent with GitHub API access",
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.github.com/mcp",
      "oauthScopes": ["repo", "user", "read:org"]
    }
  },
  "tools": ["@github"],
  "allowedTools": ["@github"]
}
```

OAuthのスコープに関するエラーが発生した場合は、空の配列を使用することでスコープ要件をバイパスできます：

json

```json
{
  "name": "github-agent",
  "description": "Agent with GitHub API access",
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.github.com/mcp",
      "oauthScopes": []
    }
  },
  "tools": ["@github"],
  "allowedTools": ["@github"]
}
```

## 効果的なカスタムエージェントを作成するためのヒント

- **シンプルに始める** - まずは基本的なツールの設定から始め、必要に応じて機能を追加していく
- **わかりやすい名前をつける** - 目的が明確に伝わるカスタムエージェント名を選択する
- **関連するコンテキストを含める** - リソースにプロジェクトのドキュメントや設定ファイルを追加する
- **安全なツールを事前に承認する** - 頻繁に使用され、リスクの低いツールを `allowedTools` に含める
- **動的なコンテキストにはフックを活用する** - コマンドフックを通じて現在のシステム状態を反映させる
- **ツールの適用範囲を制限する** - `toolsSettings` を使用して、ツールのアクセスを関連するパスやサービスに限定する
- **徹底的にテストする** - カスタムエージェントの設定が期待通りに動作することを確認する
- **カスタムエージェントを文書化する** - 明確な説明を用いて、チームメンバーがカスタムエージェントの目的を理解できるようにする

## 次のステップ

- [カスタムエージェントの作成](https://kiro.dev/docs/custom-agents/creating/)
- [設定リファレンス](https://kiro.dev/docs/custom-agents/configuration-reference/) - すべてのフィールドに関する詳細情報
- [古い設定のアップグレード](https://kiro.dev/docs/custom-agents/configuration-reference/#upgrading-from-older-configs) - ``/upgrade-agent`` を使用して v2 設定を移行する
- [トラブルシューティング](https://kiro.dev/docs/custom-agents/troubleshooting/) - よくある問題の解決方法


---

[← 前へ: Invoking as sub-agents](subagents.md) | [↑ 親ページ](index.md) | [次へ →: Troubleshooting](troubleshooting.md)
