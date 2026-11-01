# Create powers

> 元URL: https://kiro.dev/docs/powers/create/  
> 最終取り込み日: 2026-11-01  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

開発者は、Datadog、Figma、Neon、Netlify、Postman、Supabase、Stripe など、パワーレジストリから厳選されたパワーを利用できるほか、あらゆるツールやフレームワーク向けに独自のパワーを構築・共有することも可能です。また、コミュニティのメンバーは、SaaS アプリケーションの構築、AWS CDK インフラストラクチャの管理、Pydantic AI を使ったエージェントの構築のためのパワーも作成しています。

「Powers」は、AIエージェントを拡張する再利用可能なコンポーネントをパッケージ化するための、オープンでベンダー中立なフォーマットである「[Agent Plugins](https://agent-plugins.org/)」仕様に準拠しています。関連するキーワードを指定すると、Kiroは自動的にその「Powers」のコンテキストとツールを読み込みます。

厳選された「powers」は、[kiro.dev/powers](https://kiro.dev/powers/) でご覧いただけます[。](https://kiro.dev/powers/)

## 必要なもの

すべてのパワーには、`plugin.json`マニフェストが必要です。オプションで、以下を追加することもできます：

- `skills/` — タスク固有の指示を含むエージェントスキル
- `mcp.json` — ツール連携のためのMCPサーバー設定

## plugin.jsonの作成

`plugin.json`マニフェストは、パワーを識別し、Kiroにそれをいつ有効化すべきかを指示します。最低限、以下の内容が必要です：

json

```json
{
  "$schema": "https://agent-plugins.org/schemas/1.0.0/plugin.schema.json",
  "name": "supabase",
  "version": "1.0.0",
  "description": "Build fullstack applications with Supabase's Postgres database, authentication, storage, and real-time subscriptions",
  "author": {
    "name": "Supabase"
  },
  "keywords": ["database", "postgres", "auth", "storage", "realtime", "backend", "supabase", "rls"]
}
```

### 必須フィールド

| フィールド | 説明 |
| --- | --- |
| `$schema` | スキーマ URL: `https://agent-plugins.org/schemas/1.0.0/plugin.schema.json` |
| `name` | プラグイン識別子（ケバブケース、スペースなし）。Kiroが内部でパワーを識別するために使用されます。 |
| `version` | 機能のセマンティックバージョン（例：`1.0.0`）。 |
| `description` | 機能の概要。 |
| `author` | 少なくとも `name` フィールドを持つオブジェクト。`email` および `url` を含めることもできます。 |
| `keywords` | アクティベーションをトリガーする文字列の配列。開発者がツールについて話す際に使う用語を使用してください。 |

誰かが「データベースを設定しよう」と言った場合、Kiro はキーワードの中に「データベース」を認識し、Supabase パワーをアクティブにします。そのパワーの MCP ツールとスキルは、コンテキストに自動的に読み込まれます。

### オプションのフィールド

マニフェストには、以下のフィールドを含めることもできます：

json

```json
{
  "$schema": "https://agent-plugins.org/schemas/1.0.0/plugin.schema.json",
  "name": "supabase",
  "version": "1.0.0",
  "description": "Build fullstack applications with Supabase",
  "author": {
    "name": "Supabase",
    "url": "https://supabase.com"
  },
  "keywords": ["database", "postgres", "supabase"],
  "homepage": "https://supabase.com/docs",
  "repository": "https://github.com/supabase/kiro-power",
  "license": "Apache-2.0"
}
```

## スキルの追加

スキルとは、エージェントが特定のタスクを実行するための指示です。各スキルは`skills/`配下の独自のディレクトリに配置され、`SKILL.md`ファイルが必要です。

```text
skills/
└── setup/
    ├── SKILL.md
    ├── scripts/
    │   └── validate-setup.sh
    └── references/
        └── schema-patterns.md
```

### SKILL.md

`SKILL.md`ファイルには、スキルが呼び出された際にエージェントが従う手順が記載されています。明確な手順で構成してください：

マークダウン

```markdown
---
name: setup
description: Set up a new Supabase project with local development
---

# Set up Supabase

## Step 1: Validate dependencies

Before using Supabase, ensure the following are installed:
- **Docker Desktop**: Required for the local development stack
  - Verify with: `docker --version`
- **Supabase CLI**: Install via npm or Homebrew
  - Verify with: `supabase --version`

## Step 2: Initialize the project

Run `supabase init` to create the project configuration.

## Step 3: Start local services

Run `supabase start` to spin up the local Supabase stack.
```

### スクリプト

`scripts/`ディレクトリには、スキルの実行中にエージェントが実行できる実行可能ファイルが格納されます。スクリプトは、検証、セットアップの自動化、またはコード生成タスクに使用します。

### 参照

`references/`ディレクトリには、スキル実行中にエージェントが参照できるドキュメント、サンプル、スキーマ定義などの補足資料が含まれています。

## MCPサーバーの追加

プロバイダーがMCPツールを提供している場合は、プロバイダーのルートディレクトリに`mcp.json`ファイルを作成してください：

json

```json
{
  "$schema": "https://agent-plugins.org/schemas/1.0.0/mcp.schema.json",
  "mcpServers": {
    "supabase-local": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@supabase/mcp-server-supabase"],
      "env": {
        "SUPABASE_URL": "${SUPABASE_URL}",
        "SUPABASE_ANON_KEY": "${SUPABASE_ANON_KEY}"
      }
    }
  }
}
```

API キーおよびシークレットには環境変数を使用してください。インストール時、Kiro は競合を避けるためにサーバー名に自動的にネームスペースを付与します（例：`supabase-local` が `power-supabase-supabase-local` になります）。

## ディレクトリ構造

完全な power の構成は次のようになります:

```text
power-supabase/
├── plugin.json                           # Required manifest
├── mcp.json                              # MCP server configuration
└── skills/                               # Agent Skills
    ├── setup/
    │   ├── SKILL.md
    │   └── scripts/
    │       └── validate-deps.sh
    └── migrations/
        ├── SKILL.md
        └── references/
            └── migration-patterns.md
```

## ローカルでのテスト

1. 上記のファイルを使用して、Powerディレクトリを作成します
2. Kiroを開き、[Powers]パネルから[**Add Custom Power**]を選択します
3. **「フォルダからパワーをインポート」**を選択
4. パワーディレクトリを選択します
5. 会話の中でパワーに含まれるキーワードを使って、発動を確認する

## パワーの共有

パワーはリポジトリのルートに配置することも、フォルダ内に配置することもできます。各パワーが独自のフォルダを持っている限り、1つのリポジトリに複数のパワーを含めることも可能です。パワーをパブリックなGitHubリポジトリにプッシュします:

bash

```bash
git init
git add .
git commit -m "Initial release"
git push origin main
```

他のユーザーは、リポジトリのURLを使用して「**カスタムパワーを追加**」→「**GitHubからパワーをインポート」**からインストールできます。

## 例

**最小限のパワー（マニフェストのみ）：**

```text
power-simple-tool/
├── plugin.json           # Keywords and description
└── mcp.json              # MCP server configuration
```

**スキルのみのパワー（MCPなし）：**

```text
power-react-patterns/
├── plugin.json
└── skills/
    ├── component-patterns/
    │   └── SKILL.md
    └── hooks-patterns/
        └── SKILL.md
```

**すべてのコンポーネントを含む完全なパワー：**

```text
power-full-stack/
├── plugin.json
├── mcp.json
└── skills/
    ├── setup/
    │   ├── SKILL.md
    │   └── scripts/
    └── deployment/
        ├── SKILL.md
        └── references/
```

## 関連ドキュメント

- [エージェントプラグイン仕様](https://agent-plugins.org/) - オープンスタンダードの能力はこれに基づいて構築されています
- [能力の概要](https://kiro.dev/docs/powers/) - 能力とは何か、その仕組み
- [パワーのインストール](https://kiro.dev/docs/powers/installation/) - マーケットプレイスまたはGitHubからのインストール
- [スキル](https://kiro.dev/docs/skills/) - スタンドアロンのポータブルな命令パッケージ
- [MCPの設定](https://kiro.dev/docs/mcp/configuration/) - MCPサーバーの設定リファレンス


---

[← 前へ: Install powers](installation.md) | [↑ 親ページ](index.md) | [次へ →: Cloud sessions](../cloud-sessions.md)
