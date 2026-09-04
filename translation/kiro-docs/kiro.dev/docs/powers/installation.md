# Install powers

> 元URL: https://kiro.dev/docs/powers/installation/  
> 最終取り込み日: 2026-09-05  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

厳選されたパートナー、コミュニティ、またはチームのプライベートツールからパワーをインストールできます。Kiroは、[Agent Plugins](https://agent-plugins.org/)形式（`plugin.json`）とレガシー形式（`POWER.md`）の両方をサポートしており、形式にかかわらずインストール手順は同じです。

## 厳選されたパワーのインストール

Datadog、Dynatrace、Figma、Neon、Netlify、Postman、Supabase、Stripe、Strands SDK、AWS Auroraなど、公式[レジストリ](https://kiro.dev/powers/)のパワーを閲覧できます。

### kiro.devより

1. [kiro.dev/powers](https://kiro.dev/powers/) でパワーを閲覧
2. パワーを選択し、「**Kiroに追加」**をクリックしてください
3. Kiro IDEが開き、ワンクリックでインストールを完了できます

### IDE から

1. 「powers」パネルを開き、**稲妻のついたゴーストのアイコン**をクリックします
2. パワーを選択して詳細を確認
3. **「+ インストール」**を選択します

これで、**そのパワーを試して**オンボーディングを進めることができます。そのパワーに関連する質問をするたびに、Kiroエージェントが自動的に起動し、そのパワーを使用します。

### MCP を含むパワー

Model Context Protocol（MCP）の統合機能を含むパワーをインストールすると、Kiroは競合を避けるためにサーバー名に自動的にネームスペースを付与し、MCPサーバーを管理します。

- **エージェントプラグイン形式（plugin.json）：**MCPサーバーはKiroによって内部的に管理され、ユーザーレベルの`~/.kiro/settings/mcp.json`ファイルには追加されません。これらはパワーとともに有効化および無効化されます。
- **レガシー形式（POWER.md）：**MCPサーバーは、`~/.kiro/settings/mcp.json`設定ファイルの「Powers」セクションに登録されます。

## カスタムパワーのインストール

### GitHubの公開URLから

1. 「パワー」パネル → 「**カスタムパワーを追加」**
2. **「GitHubからパワーをインポート」**を選択
3. リポジトリの URL を入力
4. **「インストール」**をクリック

各パワーには、パッケージのルートディレクトリに有効な`plugin.json`または`POWER.md`ファイルが存在する必要があります。1つのリポジトリに複数のパワーを含めることができ、それぞれが独自のディレクトリに配置されます。

### ローカルパスから

プライベートリポジトリ内で作成または管理するパワーについては、リポジトリをローカルにクローンし、ローカルパスからインストールしてください。

1. 「Powers」パネル → 「**カスタムパワーを追加」**
2. **「フォルダからパワーをインポート」**を選択
3. plugin.json または POWER.md を含むパワーディレクトリを選択
4. **「インストール」**をクリック

**構造例（Agent Plugins 形式）：**

```text
my-custom-power/
├── plugin.json           # Required manifest
├── mcp.json              # Optional MCP server configuration
└── skills/               # Optional Agent Skills
    └── setup/
        └── SKILL.md
```

## パワーズの更新

パワーを最新バージョンに更新するには：

1. Powers パネル → パワー → **更新の確認**
2. 更新が利用可能な場合は、「**更新をインストール」**をクリックします

パワーがリモートリポジトリから更新され、最新バージョンが適用されます。

## 関連ドキュメント

- [Powersの概要](https://kiro.dev/docs/powers/) - Powersとは何か、その仕組み
- [Powersの作成](https://kiro.dev/docs/powers/create/) - 独自のPowersを構築する
- [MCP の設定](https://kiro.dev/docs/mcp/configuration/) - MCP サーバー設定のリファレンス


---

[← 前へ: Powers](index.md) | [↑ 親ページ](index.md) | [次へ →: Create powers](create.md)
