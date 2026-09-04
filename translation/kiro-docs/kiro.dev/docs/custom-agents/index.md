# Custom agents

> 元URL: https://kiro.dev/docs/custom-agents/  
> 最終取り込み日: 2026-09-05  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

カスタムエージェントは、さまざまなユースケースに合わせて具体的な設定を定義することで、Kiroの動作をカスタマイズする方法を提供します。各カスタムエージェントは、そのエージェントがアクセスできるツール、持つ権限、および含めるべきコンテキストを指定する設定ファイルによって定義されます。

| 機能 | IDE | CLI | Web | モバイル |
| --- | --- | --- | --- | --- |
| プロジェクトレベルのエージェント (`.kiro/agents/`) | ✓ | ✓ | ✓ | — |
| グローバルエージェント (`~/.kiro/agents/`) | ✓ | ✓ | — | — |
| UI によるエージェントの切り替え | ✓ | ✓ | — | — |
| エージェント設定の変更 | ✓ | ✓ | — | — |

デフォルトでは、Kiroは利用可能なすべてのツールへのアクセスを提供しますが、ほとんどの操作にはユーザーの確認が必要です。このアプローチはセキュリティを優先していますが、頻繁に表示される権限の確認プロンプトによってワークフローが中断される可能性があります。

カスタムエージェントでは、以下の操作が可能になることでこの問題を解決します：

- **特定のツールを事前承認する** — 確認プロンプトなしで実行できるツールを定義する
- **ツールへのアクセスを制限する** — 利用可能なツールを制限し、複雑さを軽減する
- **関連するコンテキストを含める** — プロジェクトファイル、ドキュメント、またはシステム情報を自動的に読み込む
- **ツールの動作を構成する** - ツールの動作方法に関する具体的なパラメータを設定する

## カスタムエージェントを使用するメリット

1. **ワークフローの最適化** - AWSインフラストラクチャ管理、コードレビュー、デバッグセッションなどの特定のタスクに合わせてカスタマイズされたエージェントを作成します。
2. **作業の妨げを軽減** — 信頼できるツールを事前に承認することで、集中して作業している際の権限確認のポップアップを排除します。
3. **コンテキストの充実** - 関連するプロジェクトのドキュメント、設定ファイル、またはシステム情報を自動的に含めることができます。
4. **チームでの共同作業** - カスタムエージェントの設定をチームメンバーと共有し、開発環境の一貫性を確保します。
5. **セキュリティ制御** - ツールのアクセスを特定のワークフローに必要な範囲に限定し、潜在的なセキュリティリスクを低減します。

## MCPおよび組み込みツールとの関係

カスタムエージェントは、[MCP](https://kiro.dev/docs/mcp/) サーバーの[組み込みツール](https://kiro.dev/docs/tools/)と外部ツールの両方で動作します。「`tools`」フィールドを使用して、各ソースからどのツールが利用可能かを正確に指定し、「`toolAliases`」を使用して命名上の競合に対処します。

## 設定ファイルの形式

エージェントの設定には、JSONとMarkdownの2つの形式がサポートされています。どちらの形式も同一のフィールドをサポートしています。システムプロンプトが長い場合や、人間が読みやすい形式が望ましい場合はMarkdownを使用してください。JSONは、プログラムによって生成される設定に適しています。

`.kiro/agents/my-agent.json`:

json

```json
{
  "name": "my-agent",
  "description": "A custom agent for my workflow",
  "tools": ["read", "write", "shell"],
  "excludedTools": ["knowledge"],
  "includeMcpJson": true,
  "includePowers": false,
  "resources": [
    "file://./ARCHITECTURE.md",
    "skill://backend-patterns"
  ],
  "permissions": {
    "rules": [
      { "capability": "shell", "match": ["npm *", "git *"], "effect": "allow" }
    ]
  },
  "prompt": "You are a helpful coding assistant",
  "model": "claude-sonnet-5",
  "welcomeMessage": "Ready to help. What are you working on?"
}
```

### 保存場所

- **ワークスペースエージェント（プロジェクト固有）：**`.kiro/agents/[name].json` または `.kiro/agents/[name].md` — バージョン管理を介して共有され、ワークスペースが信頼されている場合にのみ読み込まれます
- **グローバルエージェント（ユーザー全体）：**`~/.kiro/agents/[name].json` または `~/.kiro/agents/[name].md` — すべてのプロジェクトで利用可能

両方の場所に同じ名前のエージェントが存在する場合、ワークスペースエージェントが優先されます。

ネストされたディレクトリがサポートされています。エージェント名は、拡張子を除いた agents ディレクトリからの相対パスです。つまり、`~/.kiro/agents/team/planner.md` は `team/planner` となります。

#### 表面上の動作

IDE および CLI では、カスタムエージェントをプライマリセッションエージェントとして選択できます。Kiro Web は、`.kiro/agents/` にコミットされたプロジェクトレベルのカスタムエージェントを読み込み、サブエージェントへの委任のためにそれらを呼び出すことはできますが、プライマリ Web セッションエージェントとして選択することはできません。モバイル版では、Kiro の[組み込みエージェント](https://kiro.dev/docs/custom-agents/built-in/)のみが使用されます。

クラウドセッションで個人用カスタムエージェントを使用するには、[Kiro Web](https://app.kiro.dev/settings/cloud-config)の **[設定] > [同期]** からそれらをアップロードし、**[設定] > [エージェント]** で管理してください。

## 以前のバージョン

IDE 0.x または CLI 2.x からアップグレードする場合、エージェント設定は下位互換性があります。既存の JSON ファイルは変更なしで引き続き動作します。新しいフィールド（`permissions`、`excludedTools`、`includeMcpJson`、`resources`（`skill://`、Markdown 形式））はすべてオプションです。

- [IDE 0.x リファレンス - カスタムエージェント設定](https://kiro.dev/docs/ide/0x-reference/#custom-agent-config)
- [CLI 2.x リファレンス - カスタムエージェント設定](https://kiro.dev/docs/cli/2x-reference/#custom-agent-config)
- [CLI 3.0の新機能 - エージェント設定の変更](https://kiro.dev/docs/cli/v3/agent-config/)
- [IDE 1.0の新機能 - エージェント設定の変更](https://kiro.dev/docs/ide/whats-new-v1/agent-config/)

## 次の手順

- [組み込みエージェント](https://kiro.dev/docs/custom-agents/built-in/) - Kiro に同梱されている事前設定済みのエージェント
- [カスタムエージェントの作成](https://kiro.dev/docs/custom-agents/creating/)
- [設定リファレンス](https://kiro.dev/docs/custom-agents/configuration-reference/) - すべての設定フィールドに関する完全なリファレンス
- [サブエージェントとしての起動](https://kiro.dev/docs/custom-agents/subagents/) - タスクをエージェントに並行して委譲する
- [権限](https://kiro.dev/docs/permissions/) - 機能ベースのアクセス制御を設定する
- [事例](https://kiro.dev/docs/custom-agents/examples/) - 実運用におけるエージェント設定例
- [トラブルシューティング](https://kiro.dev/docs/custom-agents/troubleshooting/) - よくある問題と解決策


---

[← 前へ: Permissions](../permissions.md) | [↑ 親ページ](../../../../index.md) | [次へ →: Built-in agents](built-in.md)
