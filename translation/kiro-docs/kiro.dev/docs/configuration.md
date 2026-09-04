# Configuration scopes

> 元URL: https://kiro.dev/docs/configuration/  
> 最終取り込み日: 2026-09-05  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

Kiro は、3 つのスコープからなる階層型設定システムを採用しています。現在のコンテキストに近い設定が、より広範な設定よりも優先されます。

| 機能 | IDE | CLI | Web | モバイル |
| --- | --- | --- | --- | --- |
| グローバルスコープ (`~/.kiro/`) | ✓ | ✓ | — | — |
| プロジェクトのスコープ (`.kiro/`) | ✓ | ✓ | ✓ | ✓ |
| エージェントの範囲 (`.kiro/agents/` または `~/.kiro/agents/`) | ✓ | ✓ | — | — |

## スコープ

1. **グローバル** — すべてのプロジェクトに適用されます。`~/.kiro/` に保存されます。
2. **プロジェクト** — ワークスペースに固有です。`<project-root>/.kiro/` に保存されます。
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

概要には、エージェント、MCP サーバー、Powers、Steering、Skills、および Hooks が含まれます。設定済みの項目の数やステータスが表示され、各カテゴリを開くこともできます。`/config` は V1 および V2 では利用できません。

### ソースラベル

Kiro が設定の由来に関する情報を保持している場合、「**ソース**」列には、各項目が「`local`」、「`cloud`」、または「`local + cloud`」として識別されます。ローカルセッションにおいて、いずれの項目についてもソース情報が存在しない場合、Kiro は推測に基づいてすべての項目にラベルを付けるのではなく、その列を省略します。クラウドセッションでは、そのセッションの設定は「クラウド由来」として表示されます。

「`cloud`」というラベルは、アクティブな設定の出典元を示します。「`/config`」を開いても、ローカルファイルがアップロードされたり、クラウド設定と同期されたりすることはありません。アカウントでクラウド設定が有効になっていない場合でも、パネルは利用可能であり、クラウドにバックアップされていない項目を除いたローカル設定が表示されます。

### ローカルセッションにクラウド設定を適用する

Kiro Webの「[設定同期」](https://kiro.dev/docs/web/cloud-configuration/)機能を使用して、個人の`.kiro`ディレクトリからサポートされているフォルダをアップロードします。アップロードされた設定は、クラウドセッションに自動的に適用されます。

ローカル作業でクラウドのコピーを使用するには、「設定同期」ページで**「ローカルセッションにクラウド設定を適用する」**を有効にしてください。これにより、新しいローカル CLI セッションでは、クラウド上の Steering ファイル、カスタムエージェント、スキル、パワー、フックが読み込まれます。このオプションでは、クラウドのコンテンツがローカルの `.kiro` ディレクトリに書き込まれることはなく、既存のローカルファイルが置き換えられることもありません。

### 読み取り専用およびルーティングされたビュー

カテゴリの概要およびパネル内の「Powers」、「Steering」、「Skills」リストは読み取り専用です。「Agents」を選択すると既存のエージェントピッカーが開き、そこでエージェントを切り替えることができます。MCPサーバーおよびフックは、それぞれの既存のパネルを開きます。`/config`自体は設定ファイルを編集またはアップロードしませんが、一部のリンクされたビューはインタラクティブです。

## ファイルパス

| 設定 | グローバルスコープ | プロジェクトスコープ |
| --- | --- | --- |
| [MCP サーバー](https://kiro.dev/docs/mcp/configuration/) | `~/.kiro/settings/mcp.json` | `.kiro/settings/mcp.json` |
| [権限](https://kiro.dev/docs/permissions/) | `~/.kiro/settings/permissions.yaml` | `~/.kiro/workspace-roots/<hash>/permissions.yaml` （ユーザー単位、リポジトリ外） |
| [カスタムエージェント](https://kiro.dev/docs/custom-agents/) | `~/.kiro/agents/` | `.kiro/agents/` |
| [運営](https://kiro.dev/docs/steering/) | `~/.kiro/steering/` | `.kiro/steering/` |
| [スキル](https://kiro.dev/docs/skills/) | `~/.kiro/skills/` | `.kiro/skills/` |
| [フック](https://kiro.dev/docs/hooks/) | `~/.kiro/hooks/` | `.kiro/hooks/` |
| [パワー](https://kiro.dev/docs/powers/) | `~/.kiro/powers/` | — |
| [仕様](https://kiro.dev/docs/specs/) | — | `.kiro/specs/` |
| 設定 (CLI) | `~/.kiro/settings/cli.json` | — |

## 各スコープでサポートされる機能

すべての機能がすべてのスコープで利用できるわけではありません。以下の表は、各設定が定義できる場所と、エージェントプロファイルに埋め込むことができるかどうかを示しています。

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
| 設定 | ✓ | — | — |

## 競合の解決

複数のスコープに同じ設定が存在する場合、現在のコンテキストに最も近いスコープが優先されます:

| 設定 | 優先順位（高い順 → 低い順） |
| --- | --- |
| MCP サーバー | エージェント > プロジェクト > グローバル |
| 権限 | スコープに関係なく拒否が優先される（deny-overridesアルゴリズム） |
| カスタムエージェント | プロジェクト > グローバル（同名の場合：警告を伴いプロジェクトが優先される） |
| ステアリング | すべてのスコープを統合（上書きなし） |
| スキル | すべてのスコープをマージ |
| フック | すべてのスコープが統合されました |

MCP サーバーは 3 つのスコープで構成可能であり、エージェントには「`includeMcpJson`」設定があるため、MCP サーバーの解決には追加のルールが適用されます。詳細については、「[MCP サーバーの読み込み優先順位」](https://kiro.dev/docs/mcp/configuration/#mcp-server-loading-priority)を参照してください。

## Webおよびモバイル

Webおよびモバイルは、リポジトリにコミットされたプロジェクト設定を読み取りますが、その対象範囲は異なります。モバイルはプロジェクトのステアリングとスキルを読み取るのに対し、Kiro WebはさらにプロジェクトのMCPサーバー、カスタムエージェント、およびフックを読み取ります（下表を参照）。 ローカルファイルシステムが存在しないため、どちらのインターフェースもグローバルな`~/.kiro/`設定を読み取りません。Kiro Webでは、[Configuration](https://kiro.dev/docs/web/cloud-configuration/) Syncを通じてアップロードした選択済みの個人設定を使用することも可能です。

| 設定 | Web | モバイル |
| --- | --- | --- |
| プロジェクトのステアリング（`.kiro/steering/`） | ✓ | ✓ |
| プロジェクトのスキル（`.kiro/skills/`） | ✓ | ✓ |
| プロジェクト MCP サーバー (`.kiro/settings/mcp.json`) | ✓ | — |
| Project カスタムエージェント (`.kiro/agents/`、サブエージェントの委任) | ✓ | — |
| プロジェクトフック (`.kiro/hooks/`) | ✓ | — |
| サンドボックス MCP (Web 設定で構成) | ✓ | — |
| 権限 YAML および対話型承認 | — | — |

クラウドセッションの場合は、[Kiro](https://app.kiro.dev/settings/cloud-config) Webで**［設定］ > ［同期］**を開き、個人のSteering、カスタムエージェント、スキル、およびフックをアップロードしてください。アップロードされた項目は、各機能専用の設定ページから管理できます。この管理されたクラウド構成は、お使いのコンピュータ上の`~/.kiro/`ディレクトリとは別のものであり、ローカルファイルを変更することはありません。

Webおよびモバイルの制限の詳細については[、「カスタムエージェント：サーフェス動作」](https://kiro.dev/docs/custom-agents/#surface-behavior)を参照してください。

## 次の手順

- [設定の同期](https://kiro.dev/docs/web/cloud-configuration/) - クラウドセッション用の個人設定をアップロードする
- [カスタムエージェント](https://kiro.dev/docs/custom-agents/) — 専用エージェントの作成と設定
- [ステアリング](https://kiro.dev/docs/steering/) — プロジェクトのコンテキストと規約
- [権限](https://kiro.dev/docs/permissions/) — 機能ベースのアクセス制御
- [MCP設定](https://kiro.dev/docs/mcp/configuration/) — 外部ツールの連携
- [フック](https://kiro.dev/docs/hooks/) — イベント駆動型アクションによるワークフローの自動化
- [スキル](https://kiro.dev/docs/skills/) — 再利用可能な手順パッケージ
- [Powers](https://kiro.dev/docs/powers/) — パッケージ化されたMCPサーバーやドキュメントによる機能拡張


---

[← 前へ: Code intelligence](tools/code-intelligence.md) | [↑ 親ページ](../../../index.md) | [次へ →: IDE 1.x](/docs/ide/)
