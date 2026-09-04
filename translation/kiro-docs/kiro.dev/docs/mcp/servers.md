# Server directory

> 元URL: https://kiro.dev/docs/mcp/servers/  
> 最終取り込み日: 2026-09-05  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

外部サービス、データベース、ツールに接続するMCPサーバーを活用して、Kiroの機能を拡張しましょう。以下のディレクトリを参照し、「`Add to Kiro`」をクリックして、ワンクリックでインストールしてください。

## MCPサーバーディレクトリ

| 名称 | インストール | 説明 |
| --- | --- | --- |
| **Amazon Devices Builder Tools MCP** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=amazon-devices-buildertools-mcp&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22%40amazon-devices%2Famazon-devices-buildertools-mcp%40latest%22%5D%2C%22disabled%22%3Afalse%7D) | Builder Tools MCP は、Amazon デバイス向けアプリの開発、テスト、デバッグに必要な情報とツールを提供します。詳細および最新の機能については、[公式ドキュメント](https://developer.amazon.com/docs/vega/latest/mcp-server.html)を参照してください。[Node のインストール](https://nodejs.org/en/download)が必要です。 |
| **Amplitude** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=amplitude-mcp&config=%7B%22url%22%3A%20%22https%3A//mcp.amplitude.com/mcp%22%2C%20%22disabled%22%3A%20false%2C%20%22autoApprove%22%3A%20%5B%5D%7D) | AmplitudeのAIを活用した製品データ、実験、ユーザー行動を操作できます。 |
| **Apify** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=apify&config=%7B%22url%22%3A%22https%3A%2F%2Fmcp.apify.com%2F%22%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | Kiroを、ウェブスクレイピング、データ抽出、自動化のための[数千ものツール](https://apify.com/store)と連携させます。アクターを実行し、結果にアクセスし、Apifyのドキュメントを検索できます。 |
| **Atlassian** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=atlassian-rovo&config=%7B%22url%22%3A%20%22https%3A//mcp.atlassian.com/v1/mcp/authv2%22%2C%20%22disabled%22%3A%20false%2C%20%22autoApprove%22%3A%20%5B%5D%7D) | Atlassian Rovo MCP Server を使用して、Jira、Confluence、Compass 間で計画、追跡、コラボレーションを行います。 |
| **AWS ドキュメント** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=aws-docs&config=%7B%22command%22%3A%22uvx%22%2C%22args%22%3A%5B%22awslabs.aws-documentation-mcp-server%40latest%22%5D%2C%22env%22%3A%7B%22FASTMCP_LOG_LEVEL%22%3A%22ERROR%22%7D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | AWSドキュメントへのアクセス、検索機能、およびコンテンツのおすすめ機能を利用できます。[UVのインストール](https://docs.astral.sh/uv/getting-started/installation/)が必要です |
| **Azure** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=azure&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22%40modelcontextprotocol%2Fserver-azure%22%5D%2C%22env%22%3A%7B%22AZURE_SUBSCRIPTION_ID%22%3A%22your-subscription-id%22%7D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | Azureのサービスやリソースを操作できます。[Nodeのインストール](https://nodejs.org/en/download)が必要です |
| **BNB Chain** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=bnbchain-mcp&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22%40bnb-chain%2Fmcp%40latest%22%5D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | BNB Chain公式MCP：オンチェーンクエリ、トークン／NFT操作、Greenfieldストレージ、およびBSCおよびopBNB向けのERC-8004エージェントレジストリ。[Nodeのインストール](https://nodejs.org/en/download)が必要です |
| **Canva** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=canva-dev&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22%40canva%2Fcli%40latest%22%2C%22mcp%22%5D%7D) | Canvaアプリや連携機能の構築を支援するAI搭載の開発支援ツール。Canvaのドキュメント、App UI Kitコンポーネント、Apps SDKリソースにアクセスできます。 |
| **Chrome DevTools** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=chrome-devtools&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22chrome-devtools-mcp%40latest%22%5D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | DevTools を使用して、実行中の Chrome ブラウザを制御・検査し、自動化、デバッグ、パフォーマンス分析を行います。[Node のインストール](https://nodejs.org/en/download)が必要です |
| **CMC Agent Hub** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=cmc-skill-hub&config=%7B%22url%22%3A%22https%3A%2F%2Fmcp.coinmarketcap.com%2Fskill-hub%2Fstream%22%2C%22headers%22%3A%7B%22X-CMC-MCP-API-KEY%22%3A%22%24%7BCMC_MCP_API_KEY%7D%22%7D%7D) | CoinMarketCapのエージェントスキルハブ：AIエージェント向けの事前計算済みの仮想通貨市場データ、分析、および戦略に即活用できるシグナルを提供します。 |
| **Context7** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=context7&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22%40upstash%2Fcontext7-mcp%22%5D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | あらゆるライブラリやフレームワークの最新コードドキュメント。[Nodeのインストール](https://nodejs.org/en/download)が必要です |
| **CrowdStrike** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=crowdstrike-falcon-mcp&config=%7B%22command%22%3A%20%22uvx%22%2C%20%22args%22%3A%20%5B%22falcon-mcp%22%5D%2C%20%22env%22%3A%20%7B%22FALCON_CLIENT_ID%22%3A%20%22%24%7BFALCON_CLIENT_ID%7D%22%2C%20%22FALCON_CLIENT_SECRET%22%3A%20%22%24%7BFALCON_CLIENT_SECRET%7D%22%2C%20%22FALCON_BASE_URL%22%3A%20%22https%3A//api.crowdstrike.com%22%7D%2C%20%22disabled%22%3A%20true%2C%20%22autoApprove%22%3A%20%5B%5D%7D) | AIエージェントをCrowdStrike Falconに接続し、自動化されたセキュリティ分析と脅威ハンティングを実現します。[UVのインストール](https://docs.astral.sh/uv/getting-started/installation/)が必要です |
| **Databricks** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=databricks-sql&config=%7B%22url%22%3A%22REPLACE_WITH_YOUR_SQL_MCP_URL%22%2C%22headers%22%3A%7B%22Authorization%22%3A%22Bearer%20%24%7BDATABRICKS_ACCESS_TOKEN%7D%22%7D%2C%22disabled%22%3Atrue%7D) | Unity Catalog、Genie、Vector Search、SQL、およびAI支援開発を活用して、Databricks Data Intelligence Platform上でデータの構築、ガバナンス、クエリ実行を行います。[セットアップ](https://docs.databricks.com/aws/en/generative-ai/mcp/connect-clients)が必要です。 |
| **Datadog** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=datadog&config=%7B%22url%22%3A%22https%3A%2F%2Fmcp.datadoghq.com%2Fapi%2Funstable%2Fmcp-server%2Fmcp%3Ftoolsets%3Dcore%2Csynthetics%22%2C%22disabled%22%3Atrue%2C%22autoApprove%22%3A%5B%5D%7D) | DatadogのAIを活用した可観測性およびセキュリティプラットフォームを利用できます。 |
| **Docker** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=docker&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22%40modelcontextprotocol%2Fserver-docker%22%5D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | Dockerコンテナおよびイメージを管理します。[Nodeのインストール](https://nodejs.org/en/download)が必要です |
| **IBM Watsonx用Docling** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=docling&config=%7B%22command%22%3A%20%22uvx%22%2C%20%22args%22%3A%20%5B%22--from%22%2C%20%22docling-mcp%22%2C%20%22docling-mcp-server%22%2C%20%22--transport%22%2C%20%22stdio%22%5D%2C%20%22env%22%3A%20%7B%22DOCLING_SERVICE_URL%22%3A%20%22https%3A//%3Cyour-docling-saas%3E%22%2C%20%22DOCLING_SERVICE_API_KEY%22%3A%20%22%3Cyour-key%3E%22%2C%20%22DOCLING_CONVERSION_MODE%22%3A%20%22remote%22%7D%7D) | Docling MCP を使用して、PDF やその他のドキュメントを構造化された形式に変換し、新しいドキュメントを生成します。RAG ワークフローのサポートも含まれます。[UV のインストールおよび](https://docs.astral.sh/uv/getting-started/installation/) Docling for IBM watsonx のサブスクリプションが必要です。[公式ドキュメント](https://github.com/docling-project/docling-mcp)。 |
| **Dynatrace** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=dynatrace-mcp-server&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22%40dynatrace-oss%2Fdynatrace-mcp-server%40latest%22%5D%2C%22env%22%3A%7B%22DT_ENVIRONMENT%22%3A%22your-dynatrace-url%22%7D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | Dynatrace Observability Platform と連携します。[Node のインストール](https://nodejs.org/en/download)が必要です |
| **Elastic** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=elastic-agent-builder&config=%7B%22command%22%3A%20%22npx%22%2C%20%22args%22%3A%20%5B%22mcp-remote%22%2C%20%22%24%7BELASTIC_MCP_URL%7D%22%2C%20%22--header%22%2C%20%22Authorization%3A%20ApiKey%20%24%7BAPI_KEY%7D%22%5D%2C%20%22disabled%22%3A%20false%2C%20%22autoApprove%22%3A%20%5B%5D%7D) | 外部クライアントが Elastic Agent Builder ツールにアクセスし、Elasticsearch、AI、オブザーバビリティ、およびセキュリティプラットフォームを操作するための標準化されたインターフェースを提供します。[Node のインストール](https://nodejs.org/en/download)が必要です。 |
| **ファイルシステム** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=filesystem&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22%40modelcontextprotocol%2Fserver-filesystem%22%2C%22%2Fpath%2Fto%2Fallowed%2Fdirectory%22%5D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | 許可されたディレクトリ内での安全なファイル操作を実現します。[Nodeのインストール](https://nodejs.org/en/download)が必要です。 |
| **GCP** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=gcloud&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22%40google-cloud%2Fgcloud-mcp%22%5D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | Google Cloud Platformのリソースを管理します。[Nodeのインストール](https://nodejs.org/en/download)が必要です。 |
| **Git** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=git&config=%7B%22command%22%3A%22uvx%22%2C%22args%22%3A%5B%22mcp-server-git%22%5D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | Gitリポジトリの閲覧、検索、操作を行います。[UVのインストール](https://docs.astral.sh/uv/getting-started/installation/)が必要です |
| **GitHub** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=github-mcp-server&config=%7B%22url%22%3A%22https%3A%2F%2Fapi.githubcopilot.com%2Fmcp%2F%22%2C%22headers%22%3A%7B%22Authorization%22%3A%22Bearer%20%24%7BGITHUB_PERSONAL_ACCESS_TOKEN%7D%22%7D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | GitHubのリポジトリ、イシュー、プルリクエストを操作できます。 |
| **GitLab** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=gitlab&config=%7B%22type%22%3A%20%22http%22%2C%20%22url%22%3A%20%22%24%7BGITLAB_MCP_URL%7D%22%2C%20%22disabled%22%3A%20false%2C%20%22autoApprove%22%3A%20%5B%5D%7D) | GitLab MCPサーバーを使用すると、Kiroからイシュー、マージリクエスト、パイプラインの計画、追跡、管理を行うことができます。 |
| **Grafana Cloud** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=grafana-cloud&config=%7B%22url%22%3A%20%22https%3A//mcp.grafana.com/mcp%22%2C%20%22disabled%22%3A%20false%2C%20%22autoApprove%22%3A%20%5B%5D%7D) | Grafana Cloud を通じて Open Agentic Observability を活用できます。 |
| **IBM Watsonx Orchestrate** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=ibm-wxo-docs&config=%7B%22type%22%3A%20%22http%22%2C%20%22url%22%3A%20%22https%3A//developer.watson-orchestrate.ibm.com/mcp%22%7D) | watsonx Orchestrateに関する最新のドキュメントを検索するためのツールを利用できます。 |
| **Kubernetes** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=kubernetes&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22%40modelcontextprotocol%2Fserver-kubernetes%22%5D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | Kubernetes クラスタを操作します。[Node のインストール](https://nodejs.org/en/download)が必要です |
| **メモリ** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=memory&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22%40modelcontextprotocol%2Fserver-memory%22%5D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | AIエージェント向けのナレッジグラフベースの永続メモリシステム。[Nodeのインストール](https://nodejs.org/en/download)が必要です |
| **MongoDB** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=mongodb&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22%40modelcontextprotocol%2Fserver-mongodb%22%5D%2C%22env%22%3A%7B%22MONGODB_URI%22%3A%22your-mongodb-uri%22%7D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | MongoDBデータベースとの連携。[Nodeのインストール](https://nodejs.org/en/download)が必要です |
| **Neo4j** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=neo4j&config=%7B%22command%22%3A%22neo4j-mcp%22%2C%22env%22%3A%7B%22NEO4J_URI%22%3A%22YOUR_NEO4J_URI%22%2C%22NEO4J_USERNAME%22%3A%22YOUR_NEO4J_USERNAME%22%2C%22NEO4J_PASSWORD%22%3A%22YOUR_NEO4J_PASSWORD%22%2C%22NEO4J_DATABASE%22%3A%22YOUR_NEO4J_DATABASE%22%2C%22NEO4J_READ_ONLY%22%3A%22false%22%7D%7D) | Neo4jグラフデータベースを操作します。[neo4j-mcpのインストール](https://neo4j.com/docs/mcp/current/installation/)が必要です |
| **New Relic** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=new-relic-mcp&config=%7B%22url%22%3A%22https%3A%2F%2Fmcp.newrelic.com%2Fmcp%2F%22%7D) | New Relicの可観測性プラットフォームを使用して、アプリケーションのパフォーマンスを監視・分析します。 |
| **Pinecone** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=pinecone&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22%40pinecone-database%2Fmcp%22%5D%2C%22env%22%3A%7B%22PINECONE_API_KEY%22%3A%22your-api-key%22%7D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | セマンティック検索、RAGワークフロー、AIアプリケーション向けのベクトルデータベース。[Nodeのインストール](https://nodejs.org/en/download)が必要です |
| **PingCAP** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=tidb&config=%7B%22command%22%3A%20%22uvx%22%2C%20%22args%22%3A%20%5B%22--from%22%2C%20%22pytidb%5Bmcp%5D%22%2C%20%22tidb-mcp-server%22%5D%2C%20%22env%22%3A%20%7B%22TIDB_HOST%22%3A%20%22%24%7BTIDB_HOST%7D%22%2C%20%22TIDB_PORT%22%3A%20%22%24%7BTIDB_PORT%7D%22%2C%20%22TIDB_USERNAME%22%3A%20%22%24%7BTIDB_USERNAME%7D%22%2C%20%22TIDB_PASSWORD%22%3A%20%22%24%7BTIDB_PASSWORD%7D%22%2C%20%22TIDB_DATABASE%22%3A%20%22%24%7BTIDB_DATABASE%7D%22%2C%20%22TIDB_SSL%22%3A%20%22true%22%7D%2C%20%22disabled%22%3A%20true%7D) | HTAP、ベクトル検索、クラウドネイティブアプリケーション向けの完全な読み書きサポートを備えた分散SQLデータベース「TiDB Cloud」と連携します。[UVのインストール](https://docs.astral.sh/uv/getting-started/installation/)が必要です |
| **Plaud** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=plaud&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22%40plaud-ai%2Fmcp%40latest%22%5D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | Kiroから直接、Plaud AIの音声録音、文字起こし、AI生成のメモにアクセスできます。録音の検索、要約やアクション項目の抽出、話者ラベル付きの完全な文字起こしの取得が可能です。 |
| **Playwright** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=playwright&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22%40playwright%2Fmcp%40latest%22%5D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | Playwright によるブラウザ自動化機能で、ウェブスクレイピング、スクリーンショットの取得、テストコードの生成が可能です。[Node のインストール](https://nodejs.org/en/download)が必要です |
| **PostgreSQL** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=postgresql&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22%40modelcontextprotocol%2Fserver-postgres%22%5D%2C%22env%22%3A%7B%22POSTGRES_CONNECTION_STRING%22%3A%22your-connection-string%22%7D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | PostgreSQLデータベースのクエリ実行および管理。[Nodeのインストール](https://nodejs.org/en/download)が必要です |
| **Postman** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=postman&config=%7B%22type%22%3A%20%22http%22%2C%20%22url%22%3A%20%22https%3A//mcp.postman.com/minimal%22%2C%20%22headers%22%3A%20%7B%22Authorization%22%3A%20%22Bearer%20%24%7BPOSTMAN_API_KEY%7D%22%7D%2C%20%22disabled%22%3A%20false%2C%20%22autoApprove%22%3A%20%5B%5D%7D) | Postmanのコレクション、API、環境を操作します。リクエストの送信、ワークスペースの管理、API定義へのアクセスが可能です。 |
| **Sequential Thinking** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=sequential-thinking&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22%40modelcontextprotocol%2Fserver-sequential-thinking%22%5D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | 反復的な思考を通じて、動的かつ内省的な問題解決を行います。[Nodeのインストール](https://nodejs.org/en/download)が必要です |
| **Snowflake** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=snowflake&config=%7B%22type%22%3A%20%22http%22%2C%20%22url%22%3A%20%22%24%7BSNOWFLAKE_MCP_SERVER_URL%7D%22%2C%20%22headers%22%3A%20%7B%22Authorization%22%3A%20%22Bearer%20%24%7BPAT_TOKEN%7D%22%2C%20%22X-Snowflake-Account%22%3A%20%22%24%7BSNOWFLAKE_ACCOUNT_IDENTIFIER%7D%22%7D%2C%20%22disabled%22%3A%20false%2C%20%22autoApprove%22%3A%20%5B%5D%7D) | Snowflakeのサービスやリソースと連携します。[公式ドキュメント](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-agents-mcp) |
| **Strands Agent** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=strands-agents&config=%7B%22command%22%3A%22uvx%22%2C%22args%22%3A%5B%22strands-agents-mcp-server%22%5D%2C%22env%22%3A%7B%22FASTMCP_LOG_LEVEL%22%3A%22INFO%22%7D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | Strands Agentに関するドキュメントにアクセスします。[UVのインストール](https://docs.astral.sh/uv/getting-started/installation/)が必要です |
| **Terraform** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=terraform&config=%7B%22command%22%3A%20%22docker%22%2C%22args%22%3A%5B%22run%22%2C%22-i%22%2C%22--rm%22%2C%22-e%22%2C%22TFE_TOKEN%3D%24%7BTFE_TOKEN%7D%22%2C%22hashicorp%2Fterraform-mcp-server%22%5D%2C%22env%22%3A%7B%22TFE_TOKEN%22%3A%22%24%7BTFE_TOKEN%7D%22%7D%2C%22disabled%22%3Afalse%7D) | Terraformレジストリから、最新のTerraformプロバイダーのドキュメント、モジュール、およびSentinelポリシーにアクセスできます。HCP TerraformおよびTerraform Enterpriseのワークスペース、変数、タグ、および実行を管理します。[Dockerのインストール](https://docs.docker.com/get-docker/)が必要です |
| **Web検索** | [+ Kiroに追加](https://kiro.dev/launch/mcp/add/?name=web-search&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22%40brave%2Fbrave-search-mcp-server%22%2C%22--transport%22%2C%22stdio%22%5D%2C%22env%22%3A%7B%22BRAVE_API_KEY%22%3A%22your-api-key%22%7D%2C%22disabled%22%3Afalse%2C%22autoApprove%22%3A%5B%5D%7D) | Brave Search API を使用してウェブを検索します。[Node のインストール](https://nodejs.org/en/download)が必要です |

## MCPサーバーを共有

ユーザーがワンクリックでMCPサーバーをKiroに即座に追加できるインストールリンクを作成します。

### インストールリンクのスキーマ

**URLの形式:**

```
https://kiro.dev/launch/mcp/add?name=<server-name>&config=<url-encoded-config>
```

**クエリパラメータ:**

| パラメータ | タイプ | 必須 | 説明 |
| --- | --- | --- | --- |
| `name` | 文字列 | はい | MCP サーバーの表示名 |
| `config` | 文字列 | はい | URLエンコードされたJSON設定オブジェクト（スキーマについては「[MCP設定」](https://kiro.dev/docs/mcp/configuration/#configuration-properties)を参照） |

### インストールリンクを生成する

これらのヘルパー関数を使用して、MCPサーバーのインストールリンクをプログラムで作成します。これらのリンクは、GitHub、Webブラウザ、およびドキュメントのいずれでも共通して機能します。

**JavaScript/TypeScript:**

javascript

```javascript
function createKiroInstallLink(name, config) {
  const encodedName = encodeURIComponent(name);
  const encodedConfig = encodeURIComponent(JSON.stringify(config));
  return `https://kiro.dev/launch/mcp/add?name=${encodedName}&config=${encodedConfig}`;
}

// Example 1: Local server with command execution
const localServerLink = createKiroInstallLink('aws-docs', {
  command: 'uvx',
  args: ['awslabs.aws-documentation-mcp-server@latest'],
  env: { FASTMCP_LOG_LEVEL: 'ERROR' },
  disabled: false,
  autoApprove: []
});

// Example 2: Remote server with URL endpoint
const remoteServerLink = createKiroInstallLink('aws-knowledge', {
  url: 'https://knowledge-mcp.global.api.aws',
  disabled: false,
  autoApprove: []
});

// Example 3: Server with environment variables
const dbServerLink = createKiroInstallLink('postgresql', {
  command: 'npx',
  args: ['-y', '@modelcontextprotocol/server-postgres'],
  env: { POSTGRES_CONNECTION_STRING: 'postgresql://localhost:5432/mydb' },
  disabled: false,
  autoApprove: []
});
```

**Python:**

python

```python
import json
from urllib.parse import urlencode, quote

def create_kiro_install_link(name: str, config: dict) -> str:
    """
    Creates a Kiro install link for one-click MCP server installation
    
    Args:
        name: Display name for the MCP server
        config: MCP server configuration dictionary
        
    Returns:
        Formatted HTTPS install link URL
    """
    encoded_name = quote(name)
    encoded_config = quote(json.dumps(config))
    return f"https://kiro.dev/launch/mcp/add?name={encoded_name}&config={encoded_config}"

# Example usage
link = create_kiro_install_link('my-server', {
    'command': 'npx',
    'args': ['-y', '@myorg/my-mcp-server'],
    'disabled': False,
    'autoApprove': []
})
```

**コマンドライン (Bash):**

bash

```bash
#!/bin/bash

# Function to create Kiro install link
create_kiro_link() {
  local name="$1"
  local config="$2"
  
  # URL encode the parameters
  local encoded_name=$(printf %s "$name" | jq -sRr @uri)
  local encoded_config=$(printf %s "$config" | jq -sRr @uri)
  
  echo "https://kiro.dev/launch/mcp/add?name=${encoded_name}&config=${encoded_config}"
}

# Example usage
CONFIG='{"command":"npx","args":["-y","@modelcontextprotocol/server-git"],"disabled":false,"autoApprove":[]}'
create_kiro_link "git" "$CONFIG"
```

### Kiroバッジを追加する

プロジェクトの README やドキュメントに `Add to Kiro` のバッジを記載することで、ユーザーがワンクリックで MCP サーバーをインストールできるようにします:

html

```html
<a href="https://kiro.dev/launch/mcp/add?name=my-server&config=%7B%22command%22%3A%22npx%22...%7D">
  <img src="https://kiro.dev/images/add-to-kiro.svg" alt="Add to Kiro" />
</a>
```

**Markdown:**

markdown

```markdown
[![Add to Kiro](https://kiro.dev/images/add-to-kiro.svg)](https://kiro.dev/launch/mcp/add?name=my-server&config=%7B%22command%22%3A%22npx%22...%7D)
```

クリックすると、サーバー設定が事前に入力された状態で Kiro を開くようユーザーに促されます。プロンプトが機能しない場合、ページにはサーバー名と再試行ボタンが表示されます。

## その他のMCPサーバーを探す

上記のディレクトリには厳選されたサーバーが掲載されていますが、MCPエコシステムには他にも数百ものサーバーが存在します。

### 公式リソース

- **[MCPレジストリ](https://github.com/modelcontextprotocol/registry)** - コミュニティから提供されたMCPサーバーの公式レジストリを閲覧できます。詳細なドキュメントやインストール手順も掲載されています。
- **[Model Context Protocol Organization](https://github.com/modelcontextprotocol)** - MCPチームが管理するリファレンス実装や公式サーバーをご覧ください。

### パッケージレジストリ

- **[npm (Node.js)](https://www.npmjs.com/search?q=mcp-server)** - 「`mcp-server`」または「`@modelcontextprotocol/server-*`」を検索して、JavaScript/TypeScript 実装を見つけてください。
- **[PyPI (Python)](https://pypi.org/search/?q=mcp-server)** - 「`mcp-server`」または名前に「MCP」を含むパッケージを検索して、Python 実装を見つけましょう。


---

[← 前へ: Configuration](configuration.md) | [↑ 親ページ](index.md) | [次へ →: Tools](usage.md)
