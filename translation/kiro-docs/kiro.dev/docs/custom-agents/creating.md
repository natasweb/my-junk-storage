# Creating custom agents

> 元URL: https://kiro.dev/docs/custom-agents/creating/  
> 最終取り込み日: 2026-11-01  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

カスタムエージェントを使用すると、利用可能なツール、付与される権限、および自動的に含まれるコンテキストを定義することで、特定のタスクに合わせてKiroの動作をカスタマイズできます。

## クイックスタート

プロジェクト内で直接エージェントファイルを作成します:

1. ワークスペース内に`.kiro/agents/`ディレクトリを作成します（グローバルエージェントの場合は`~/.kiro/agents/`）。
2. Markdown または JSON ファイルを追加します。ファイル名がエージェント名になります

例：`.kiro/agents/backend-dev.md` を作成する場合：

markdown

```markdown
---
name: backend-dev
description: Backend development specialist
model: claude-sonnet-4.6
tools: ["read", "write", "shell"]
permissions:
  rules:
    - capability: shell
      match: ["npm *", "git *"]
      effect: allow
---

You are a backend engineer focused on Node.js and TypeScript.
Always use async/await. All database queries must be parameterized.
```

その後、チャットペインのヘッダーにあるエージェントピッカーを使用して、そのエージェントに切り替えます。

## エージェント設定ファイル

カスタムエージェントは、JSONまたはMarkdownの設定ファイルを使用して定義されます。以下に基本的なJSONの例を示します：

json

```json
{
  "name": "my-agent",
  "description": "A custom agent for my workflow",
  "tools": ["read", "write"],
  "permissions": {
    "rules": [{ "capability": "fs_read", "effect": "allow" }]
  },
  "resources": [
    "file://README.md",
    "file://.kiro/steering/**/*.md",
    "skill://.kiro/skills/**/SKILL.md"
  ],
  "prompt": "You are a helpful coding assistant",
  "model": "claude-sonnet-4.6"
}
```

利用可能なすべてのフィールドに関する詳細については、「[設定リファレンス](https://kiro.dev/docs/custom-agents/configuration-reference/)」を参照してください。

## ファイルの保存場所

ローカルエージェントとグローバルエージェントを定義できます。

### ローカルエージェント（プロジェクト固有）

```text
.kiro/agents/
```

ローカルエージェントは現在のワークスペースに固有のものであり、そのディレクトリまたはそのサブディレクトリから Kiro を実行している場合にのみ利用可能です。

**例：**

```text
my-project/
├── .kiro/
│   └── agents/
│       ├── dev-agent.json
│       └── aws-specialist.json
└── src/
    └── main.py
```

### グローバルエージェント（ユーザー全体）

```text
~/.kiro/agents/
```

グローバルエージェントは、どのディレクトリからでも利用可能です。

**例：**

```text
~/.kiro/agents/
├── general-assistant.json
├── code-reviewer.json
└── documentation-writer.json
```

### エージェントの優先順位

Kiroがエージェントを検索する際：

1. **ローカル優先**：現在のディレクトリ内の`.kiro/agents/`を確認します
2. **グローバルフォールバック**：HOMEディレクトリ内の`~/.kiro/agents/`を確認します

両方の場所に同名のエージェントが存在する場合、ローカルのエージェントが優先され、警告メッセージが表示されます。

## カスタムエージェントの使用

チャット入力画面の下部バーにあるエージェント名（例：「Default」）をクリックしてエージェントピッカーを開き、リストからカスタムエージェントを選択します。この切り替えは現在のセッションに適用され、エージェントのツール、権限、プロンプトは次のメッセージから有効になります。

また、新しいセッションを開始する際に、ワークフローオプションからカスタムエージェントを選択することもできます。

## 次の手順

- [設定リファレンス](https://kiro.dev/docs/custom-agents/configuration-reference/) - 利用可能なすべての設定オプションを確認する
- [組み込みエージェント](https://kiro.dev/docs/custom-agents/built-in/) - Kiroの事前設定済みエージェントの構成方法を確認する
- [事例](https://kiro.dev/docs/custom-agents/examples/) - 実際のエージェント設定例
- [トラブルシューティング](https://kiro.dev/docs/custom-agents/troubleshooting/) - よくある問題の解決方法


---

[← 前へ: Built-in agents](built-in.md) | [↑ 親ページ](index.md) | [次へ →: Configuration reference](configuration-reference.md)
