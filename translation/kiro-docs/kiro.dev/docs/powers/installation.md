# Install powers

> 元URL: https://kiro.dev/docs/powers/installation/  
> 最終取り込み日: 2026-11-01  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

厳選されたパートナー、コミュニティ、チームのプライベートツール、またはローカルディレクトリからパワーをインストールして管理できます。Kiroは、[Agent Plugins](https://agent-plugins.org/)形式（`plugin.json`）とレガシー形式（`POWER.md`）の両方をサポートしており、形式にかかわらずインストール手順は同じです。

## 厳選されたパワーのインストール

Datadog、Dynatrace、Figma、Neon、Netlify、Postman、Supabase、Stripe、Strands SDK、AWS Auroraなど、[公式レジストリから](https://kiro.dev/powers/)パワーを閲覧できます。

### kiro.devより

1. [kiro.dev/powers](https://kiro.dev/powers/)でパワーを閲覧
2. パワーを選択し、「**Kiroに追加」**をクリックしてください
3. Kiro IDEが開き、ワンクリックでインストールを完了できます

### IDE から

1. 「powers」パネルを開き、**稲妻の付いたゴーストのアイコン**をクリックします
2. 「**利用可能なパワー**」で、検索アイコンを選択し、表示名でフィルタリングするために文字を入力します。一致する項目がない場合、Kiroは「結果なし」というメッセージを表示します。
3. パワーを選択して詳細を表示します
4. **「+ インストール」**を選択します

これで、**パワーを試して**オンボーディングを進めることができます。そのパワーに関連する質問をするたびに、Kiroエージェントが自動的にそのパワーを有効にして使用します。

### MCP を含むパワー

Model Context Protocol（MCP）の統合を含むパワーをインストールすると、Kiroは競合を避けるためにサーバー名に自動的にネームスペースを付与し、MCPサーバーを自動的に管理します。

- **エージェントプラグイン形式（plugin.json）：**MCPサーバーはKiroによって内部的に管理され、ユーザーレベルの`~/.kiro/settings/mcp.json`ファイルには追加されません。これらはパワーとともに有効化および無効化されます。
- **レガシー形式（POWER.md）：**MCP サーバーは、`~/.kiro/settings/mcp.json` 設定ファイルの「Powers」セクションに登録されます。

## カスタムパワーのインストール

### GitHubの公開URLから

1. Powers パネル → **カスタムパワーの追加**
2. **「GitHub からパワーをインポート」**を選択
3. リポジトリの URL を入力
4. **「インストール」**をクリック

各パワーには、パッケージのルートディレクトリに有効な `plugin.json` または `POWER.md` ファイルが存在する必要があります。1つのリポジトリに複数のパワーを含めることができ、それぞれが独自のディレクトリに配置されます。

### ローカルパスから

プライベートリポジトリで作成または管理するパワーについては、リポジトリをローカルにクローンし、ローカルパスからインストールしてください。

1. Powers パネル → **カスタム Power の追加**
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

Kiro IDE 1.4.26 以降では、「インストール済み」リストおよびパワーの詳細ビューに、インストールされたパワーのマニフェストからのメタデータが表示されます。Kiro は、`plugin.json` が存在する場合、それを読み込みます。存在しない場合は、`POWER.md` のフロントマターを読み込みます。 description、author、repository、または keywords の値が宣言されている場合、カタログの値はそれらに置き換えられます。マニフェストで省略されたフィールドについては、カタログの値が保持されます。Kiro は、HTTP または HTTPS を使用している場合にのみ、リポジトリへのリンクを表示します。

## パワーの更新

パワーを最新バージョンに更新するには：

1. 「Powers」パネル → パワー → **「更新を確認」**
2. 更新が利用可能な場合は、「**更新をインストール」**をクリックします

パワーはリモートリポジトリから更新され、最新バージョンが適用されます。

## セキュリティと信頼

Powerは、Kiroにスキル、スクリプト、およびMCPサーバーを追加できます。Powerからのツール呼び出しは、他のツールと同様にKiroの権限設定に従います。ローカルMCPサーバーは、サンドボックス化しない限り、ユーザーのアクセス権限でマシン上で実行されるため、ユーザーが読み取り可能なコードの実行やファイルの読み取りが可能です。 インストールする前に、パワーのソースと、それが要求するアクセス権を確認してください。パワーを削除するには、IDEの「Powers」パネルでそのパワーの**「アンインストール」**を選択するか、Kiro CLIで`/powers uninstall <name>`または`kiro-cli powers uninstall <name>`を実行してください。MCPサーバーの安全性については、[「MCPセキュリティのベストプラクティス」](https://kiro.dev/docs/mcp/security/)を参照してください。

## 関連ドキュメント

- [権限の概要](https://kiro.dev/docs/powers/) - 権限とは何か、その仕組み
- [パワーの作成](https://kiro.dev/docs/powers/create/) - 独自のパワーを構築する
- [MCP の設定](https://kiro.dev/docs/mcp/configuration/) - MCP サーバー設定のリファレンス


---

[← 前へ: Powers](index.md) | [↑ 親ページ](index.md) | [次へ →: Create powers](create.md)
