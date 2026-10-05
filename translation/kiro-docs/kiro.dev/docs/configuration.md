# Configuration scopes

> 元URL: https://kiro.dev/docs/configuration/  
> 最終取り込み日: 2026-10-06  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

Kiro は、3 つのスコープを持つ階層型設定システムを採用しています。現在のコンテキストに近い設定が、より広範な設定よりも優先されます。

| 機能 | IDE | CLI | Web | モバイル |
| --- | --- | --- | --- | --- |
| グローバルスコープ (`~/.kiro/`) | ✓ | ✓ | — | — |
| [クラウド構成](https://kiro.dev/docs/web/cloud-configuration/) | オプトイン | オプトイン | ✓ | — |
| プロジェクトスコープ (`.kiro/`) | ✓ | ✓ | ✓ | ✓ |
| エージェントのスコープ (`.kiro/agents/` または `~/.kiro/agents/`) | ✓ | ✓ | — | — |

## スコープ

1. **グローバル** — すべてのプロジェクトに適用されます。`~/.kiro/` に保存されます。
2. **プロジェクト** — ワークスペース固有。`<project-root>/.kiro/` に保存されます。
3. **エージェント** — `~/.kiro/agents/`（グローバル）または `.kiro/agents/`（プロジェクト）でエージェントごとに定義されます。

## V3での設定の確認

ローカルまたはクラウド上の V3 セッションで、`/config` を実行して、そのセッションで使用されている設定を確認します:

bash

```bash
# Open the category summary
/config

# Open a category directly
/config steering
```

概要には、エージェント、MCP サーバー、Powers、Steering、Skills、および Hooks が含まれます。設定済みの項目の数やステータスが表示され、各カテゴリを開くことができます。`/config` は V1 および V2 では利用できません。

### ソースラベル

Kiro が設定の由来に関する情報を保持している場合、「**ソース**」列には、各項目が「`local`」、「`cloud`」、または「`local + cloud`」として識別されます。ローカルセッションにおいて、いずれの項目についてもソース情報が存在しない場合、Kiro は推測に基づいてすべての項目にラベルを付けるのではなく、その列を省略します。クラウドセッションでは、そのセッションの設定は「クラウド由来」として表示されます。

「`cloud`」というラベルは、アクティブな設定の出典を示します。「`/config`」を開いても、ローカルファイルがアップロードされたり、クラウド設定と同期されたりすることはありません。アカウントでクラウド設定が有効になっていない場合でも、パネルは利用可能であり、クラウドにバックアップされた項目を除いたローカル設定が表示されます。

### ローカルセッションにクラウド設定を適用する

Kiro Webの「[設定同期」](https://kiro.dev/docs/web/cloud-configuration/)を使用して、個人の`.kiro`ディレクトリからサポートされているフォルダをアップロードします。アップロードされた設定は、クラウドセッションに自動的に適用されます。

ローカル作業でクラウドのコピーを使用するには、「設定同期」ページで**「クラウド設定をローカルセッションに適用する」**を有効にしてください。これにより、新しいローカル CLI セッションでは、クラウド上の Steering ファイル、カスタムエージェント、スキル、パワー、フックが読み込まれます。このオプションでは、クラウドのコンテンツがローカルの `.kiro` ディレクトリに書き込まれることはなく、既存のローカルファイルが置き換えられることもありません。

### 読み取り専用およびルーティングされたビュー

カテゴリの概要およびパネル内の「Powers」、「Steering」、「Skills」リストは読み取り専用です。「Agents」を選択すると既存のエージェントピッカーが開き、そこでエージェントを切り替えることができます。MCPサーバーおよびフックは、それぞれの既存のパネルを開きます。`/config`自体は設定ファイルの編集やアップロードを行いませんが、一部のリンクされたビューはインタラクティブです。

## ファイルパス

| 設定 | グローバルスコープ | プロジェクトスコープ |
| --- | --- | --- |
| [MCP サーバー](https://kiro.dev/docs/mcp/configuration/) | `~/.kiro/settings/mcp.json` | `.kiro/settings/mcp.json` |
| [権限](https://kiro.dev/docs/permissions/) | `~/.kiro/settings/permissions.yaml` | `~/.kiro/workspace-roots/<hash>/permissions.yaml` （ユーザー単位、リポジトリ外） |
| [カスタムエージェント](https://kiro.dev/docs/custom-agents/) | `~/.kiro/agents/` | `.kiro/agents/` |
| [ステアリング](https://kiro.dev/docs/steering/) | `~/.kiro/steering/` | `.kiro/steering/` |
| [スキル](https://kiro.dev/docs/skills/) | `~/.kiro/skills/` | `.kiro/skills/` |
| [フック](https://kiro.dev/docs/hooks/) | `~/.kiro/hooks/` | `.kiro/hooks/` |
| [パワー](https://kiro.dev/docs/powers/) | `~/.kiro/powers/` | — |
| [仕様](https://kiro.dev/docs/specs/) | — | `.kiro/specs/` |
| [ワークフロー](https://kiro.dev/docs/workflows/) | `~/.kiro/workflows/` | `.kiro/workflows/` |
| 設定 (CLI) | `~/.kiro/settings/cli.json` | — |

## 各スコープでサポートされる機能

すべての機能がすべてのスコープで利用できるわけではありません。以下の表は、各設定がどこで定義できるか、およびエージェントプロファイルに組み込めるかどうかを示しています。

| 設定 | グローバル | プロジェクト | エージェントスコープ |
| --- | --- | --- | --- |
| MCPサーバー | ✓ | ✓ | ✓（`mcpServers`フィールドまたは`includeMcpJson`） |
| 権限 | ✓ | ✓ | ✓（`permissions`フィールド） |
| カスタムエージェント | ✓ | ✓ | 該当なし |
| ステアリング | ✓ | ✓ | ✓（`resources`フィールド） |
| スキル | ✓ | ✓ | ✓（`resources`フィールド） |
| フック | ✓ | ✓ | ✓ (CLIのみ、`hooks`フィールド) |
| 能力 | ✓ | — | ✓（`includePowers`フィールド） |
| 仕様 | — | ✓ | — |
| ワークフロー | ✓ | ✓ | — |
| 設定 | ✓ | — | — |

## 競合の解決

複数のスコープに同じ設定が存在する場合、現在のコンテキストに最も近いスコープが優先されます:

| 設定 | 優先度（高い順 → 低い順） |
| --- | --- |
| MCP サーバー | エージェント > プロジェクト > グローバル |
| 権限 | スコープに関係なく拒否が優先される（deny-overridesアルゴリズム） |
| カスタムエージェント | プロジェクト > グローバル（同名の場合：警告を表示してプロジェクトが優先される） |
| ステアリング | すべてのスコープを統合（上書きなし） |
| スキル | すべてのスコープをマージ |
| フック | すべてのスコープがマージされました |
| ワークフロー | プロジェクト > グローバル > 同期されたアカウントのレシピ > バンドルされたレシピ |

無効な高優先度のワークフロー・レシピは、同じ名前を持つ有効な低優先度のレシピを非表示にしません。利用可能な同期済みおよびバンドル済みレシピは、アカウントやクライアントのバージョンによって異なる場合があります。

MCPサーバーは3つのスコープで構成可能であり、エージェントには「`includeMcpJson`」設定があるため、MCPサーバーの解決には追加のルールが適用されます。詳細については、「[MCPサーバーの読み込み優先順位」](https://kiro.dev/docs/mcp/configuration/#mcp-server-loading-priority)を参照してください。

## Webおよびモバイル

WebおよびMobileは、クローンされたリポジトリから以下に挙げるプロジェクト設定を読み取りますが、その対象範囲は異なります。Mobileはプロジェクトの「Steering」と「Skills」を読み取りますが、Kiro WebはさらにプロジェクトのMCPサーバー、カスタムエージェント、およびフックも読み取ります。ローカルファイルシステムが存在しないため、どちらのインターフェースもグローバルな`~/.kiro/`設定を読み取りません。

| 設定 | Web | モバイル |
| --- | --- | --- |
| プロジェクトのステアリング (`.kiro/steering/`) | ✓ | ✓ |
| プロジェクトのスキル (`.kiro/skills/`) | ✓ | ✓ |
| プロジェクト MCP サーバー (`.kiro/settings/mcp.json`) | ✓ | — |
| Project カスタムエージェント (`.kiro/agents/`、サブエージェントの委任) | ✓ | — |
| プロジェクトフック (`.kiro/hooks/`) | ✓ | — |
| サンドボックス MCP（Web 設定で構成） | ✓ | — |
| 権限のYAMLおよび対話型承認 | — | — |

### アカウントからアップロードされたワークフロー

ワークフローは、Kiro Web 内で別のアカウントアップロードパスを使用します：

| メソッド | Web | モバイル |
| --- | --- | --- |
| **[設定] > [ワークフロー]** からレシピを1つアップロードする | ✓ | 記載なし |
| [構成同期](https://kiro.dev/docs/web/cloud-configuration/)を通じて`.kiro/workflows/`をインポートする | ✓ | ドキュメントに記載なし |

クラウドセッションの場合は、[Kiro](https://app.kiro.dev/settings/cloud-config) Webで**「設定」＞「同期」**を開き、個人のSteering、カスタムエージェント、フック、スキル、パワー、MCP設定、または`.kiro/workflows/`フォルダ全体をアップロードします。アップロードされた項目は、各機能固有の設定ページから管理できます。この管理対象のクラウド構成は、お使いのコンピュータ上の`~/.kiro/`ディレクトリとは別のものであり、ローカルファイルを変更することはありません。

Webおよびモバイル版の制限事項の詳細については[、「カスタムエージェント：サーフェス動作」](https://kiro.dev/docs/custom-agents/#surface-behavior)を参照してください。

## 次の手順

- [ワークフロー](https://kiro.dev/docs/workflows/) — 再利用可能な多段階のエージェント作業を調整する
- [設定の同期](https://kiro.dev/docs/web/cloud-configuration/) — クラウドセッション用の個人設定をアップロード
- [カスタムエージェント](https://kiro.dev/docs/custom-agents/) — 専用エージェントの作成と設定
- [ステアリング](https://kiro.dev/docs/steering/) — プロジェクトのコンテキストと規約
- [権限](https://kiro.dev/docs/permissions/) — 機能ベースのアクセス制御
- [MCP設定](https://kiro.dev/docs/mcp/configuration/) — 外部ツールとの連携
- [フック](https://kiro.dev/docs/hooks/) — イベント駆動型のアクションによるワークフローの自動化
- [スキル](https://kiro.dev/docs/skills/) — 再利用可能なインストラクションパッケージ
- [Powers](https://kiro.dev/docs/powers/) — パッケージ化されたMCPサーバーやドキュメントによる機能拡張


---

[← 前へ: Code intelligence](tools/code-intelligence.md) | [↑ 親ページ](../../../index.md) | [次へ →: IDE 1.x](/docs/ide/)
