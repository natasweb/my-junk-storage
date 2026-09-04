# Agent Skills

> 元URL: https://kiro.dev/docs/skills/  
> 最終取り込み日: 2026-09-05  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

## スキルとは何ですか？

スキルとは、オープンな「[Agent Skills」](https://agentskills.io)標準に準拠した、移植可能な指示パッケージのことです。これらは、指示、スクリプト、テンプレートを再利用可能なパッケージにまとめ、タスクに関連する際にKiroがそれらを起動できるようにしたものです。

| 機能 | IDE | CLI | Web | モバイル |
| --- | --- | --- | --- | --- |
| スキルの有効化 | ✓ | ✓ | ✓ | ✓ |
| ワークスペースのスキル (`.kiro/skills/`) | ✓ | ✓ | ✓ | ✓ |
| グローバルスキル (`~/.kiro/skills/`) | ✓ | ✓ | — | — |

KiroはAgent Skills標準に対応しているため、コミュニティや他の互換性のあるAIツールからスキルをインポートしたり、独自のエコシステム全体でスキルを共有したりすることができます。

## スキルの仕組み

AIエージェントの能力は向上し続けていますが、実際の業務に必要な具体的なコンテキストが欠けていることがよくあります。チームのデプロイプロセス、会社のコードレビュー基準、プロジェクトのデータ分析パイプラインに関する知識がない場合、エージェントは推測と試行錯誤を繰り返します。これは、あなたが新しいことを学ぶときと同じです。

こうした文脈情報をすべて最初から読み込むことも現実的ではありません。情報が多すぎるとエージェントが処理しきれず、応答が遅くなり、品質が低下してしまいます。

スキルは「段階的な情報開示」によってこの問題を解決します：

1. **発見（Discovery）** — 起動時、Kiroは各スキルの名前と説明のみを読み込みます
2. **アクティベーション** — リクエストがスキルの説明と一致すると、Kiroは完全な手順を読み込みます
3. **実行** — Kiroは指示に従い、スクリプトや参照ファイルは必要な場合にのみ読み込みます

これにより、コンテキストを絞り込みつつ、Kiroが必要に応じて広範な専門知識にアクセスできるようになります。

## スキルの使用方法

スキルの起動には2つの方法があります：

- **自動** - Kiroはリクエストをスキルの説明と照合し、関連するスキルをロードします
- **スラッシュコマンドとして** - 「`/`」の後にスキル名を入力して、スキルを直接呼び出します

Kiroは、リクエストがスキルの説明と一致した場合、自動的にスキルを起動します。また、チャット入力欄に「`/`」と入力して利用可能なスキルをスラッシュコマンドとして表示し、スキルを直接呼び出すこともできます。スラッシュコマンドを選択すると、スキルの完全な説明が読み込まれ、スキルがいつ起動するかを明確に制御できるようになります。

スキル名の後にテキストを追加することも可能です（例：`/explain-file src/api/client.ts`）。このテキストは追加のコンテキストとしてエージェントに渡されます。`$ARGUMENTS` および `${N}` によるスキル本文へのプレースホルダーの置換は、現時点では CLI でのみ利用可能です。「[スキルへの引数の渡し方](https://kiro.dev/docs/skills/#passing-arguments-to-a-skill)」を参照してください。

Kiroパネルの「**Agent Steering & Skills**」セクションで、スキルを表示および管理できます。

## スキルのスコープ

スキルは、ワークスペーススコープまたはグローバルスコープで作成できます。

| 場所 | スコープ | ユースケース |
| --- | --- | --- |
| `.kiro/skills/` | ワークスペース | プロジェクト固有のワークフロー、チームの慣例 |
| `~/.kiro/skills/` | グローバル | すべてのプロジェクトにまたがる個人用ワークフロー |

スキル名が同じ場合、ワークスペースのスキルがグローバルスキルよりも優先されます。これにより、すべてのワークスペースに一般的に適用されるグローバルスキルを定義しつつ、特定のプロジェクトではそれらを上書きする機能も維持できます。

### カスタムエージェントとスキル

json

```json
{
  "name": "my-agent",
  "resources": [
    "skill://.kiro/skills/*/SKILL.md",
    "skill://~/.kiro/skills/*/SKILL.md"
  ]
}
```

`skill://` URIスキーマは、特定のパス、globパターン、およびホームディレクトリの展開に対応しています。

## スキルのインポート

1. Kiroパネルの「**エージェント制御とスキル」**セクションを開きます
2. **「+」**をクリックし、「**スキルのインポート」**を選択します
3. ソースを選択します：
   - **GitHub** - 公開リポジトリのURLからインポートします。スキルフォルダ、または`SKILL.md`ファイルに直接リンクするURLを貼り付けることができます。URLはリポジトリのルートではなく、リポジトリ内のサブディレクトリを指している必要があります。
   - **ローカルフォルダ** - ファイルシステムからインポート

インポートされたスキルはスキルディレクトリにコピーされ、すぐに利用可能になります。

## スキルの作成

スキルとは、`SKILL.md`ファイルを含むフォルダのことです：

```text
my-skill/
├── SKILL.md           # Required
├── scripts/           # Optional executable code
├── references/        # Optional documentation
└── assets/            # Optional templates
```

### SKILL.md の形式

ファイルはYAMLのフロントマターで始まり、その後にマークダウンの記述が続きます：

マークダウン

```markdown
---
name: pr-review
description: Review pull requests for code quality, security issues, and test coverage. Use when reviewing PRs or preparing code for review.
---

## Review checklist

When reviewing a pull request:

1. Check for vulnerabilities, injection risks, exposed secrets
2. Verify edge cases and failure modes are handled
3. Confirm new code has appropriate tests
4. Ensure variables and functions have clear names

## Common issues to flag

- Hardcoded credentials or API keys
- Missing input validation
- Unhandled promise rejections
- Console.log statements left in production code
```

### フロントマターのフィールド

| フィールド | 必須 | 説明 |
| --- | --- | --- |
| `name` | はい | フォルダ名と一致する必要があります。小文字、数字、ハイフンのみ使用可能です（最大64文字）。 |
| `description` | はい | このスキルの使用タイミング。Kiroはリクエストとこれを照合します（最大1024文字）。 |
| `license` | いいえ | ライセンス名、またはバンドルされたライセンスファイルへの参照。 |
| `compatibility` | いいえ | 環境要件（例：必要なツール、ネットワークアクセスなど）。 |
| `metadata` | なし | 作成者やバージョンなどの追加のキー・バリューデータ。 |

詳細なフィールドの制約については、[完全な仕様](https://agentskills.io/specification)書を参照してください。

### 参照ファイル

詳細なドキュメントについては、`references/`フォルダを使用してください：

```text
aws-deployment/
├── SKILL.md
└── references/
    ├── ecs-guide.md
    └── troubleshooting.md
```

SKILL.md 内でこれらのファイルを参照してください:

markdown

```markdown
For ECS deployments, follow the guide in `references/ecs-guide.md`.
```

Kiroは、指示がある場合にのみ参照ファイルを読み込みます。

## 例

CDKデプロイメントスキル:

```text
cdk-deploy/
├── SKILL.md
└── references/
    └── stack-patterns.md
```

**SKILL.md:**

markdown

```markdown
---
name: cdk-deploy
description: Deploy AWS CDK stacks with best practices. Use when deploying infrastructure, running cdk deploy, or troubleshooting CDK issues.
---

## Deployment workflow

1. Run `cdk synth` to validate templates before deploying
2. Use `cdk diff` to preview what will change
3. Run `cdk deploy` and review IAM changes

## Pre-deployment checks

- Verify AWS credentials are configured for the target account
- Check that the CDK version matches the project's requirements
- Review `references/stack-patterns.md` for environment-specific patterns

## Rollback procedure

If deployment fails:
1. Check CloudFormation console for the specific error
2. Run `cdk destroy` only if the stack is in a failed state
3. Fix the issue and redeploy
```

使用方法:

bash

```bash
> Deploy my CDK stack to staging

I'll follow the deployment workflow. First, let me synthesize the templates...
```

## スキルとステアリングおよびパワーの違い

**スキルは**、オープンスタンダードに準拠したポータブルなパッケージです。オンデマンドで読み込まれ、スクリプトを含めることができます。共有したい、あるいは他者からインポートしたい再利用可能なワークフローに使用します。

**[ステアリングは、](https://kiro.dev/docs/steering/)**エージェントの挙動を形作るKiro固有のコンテキストです。「`always`」、「`auto`」、「`fileMatch`」、「`manual`」の各モードをサポートしています。プロジェクトの標準や規約に活用してください。

**[Powersは、](https://kiro.dev/docs/powers/)**MCPツールにナレッジやワークフローを統合したものです。コンテキストに基づいて動的に作動します。ツールとガイダンスの両方が必要な統合に活用してください。

## ベストプラクティス

**正確な説明文を作成する** - 説明文によって、Kiroがスキルをいつ起動するかが決まります。リクエストの表現方法に合致する具体的なキーワードやアクションを含めてください：

- 良い例：`Review pull requests for security vulnerabilities and test coverage. Use when reviewing PRs or preparing code for review.`
- 曖昧な例：`Helps with code review`

**SKILL.mdは要点を絞る** - 詳細な参考資料は`references/`ファイルに記述してください。Kiroは起動時にSKILL.md全体を読み込むため、実行可能な内容に留めてください。

**確定的なタスクにはスクリプトを使用しましょう**。検証、ファイル生成、API呼び出しなどは、LLMが生成するコードよりもスクリプトとして実装した方が効果的です。

**適切なスコープを選択する** - どこでも使用する個人的なワークフロー（レビューチェックリストなど）には「Global」を、チームの手順やプロジェクト固有の規約には「Workspace」を使用する。

**ワークスペースのスキルはバージョン管理する** - チーム全員が同じワークフローを共有できるよう、`.kiro/skills/`をリポジトリにコミットしてください。

## トラブルシューティング

| 問題 | 解決策 |
| --- | --- |
| スキルが有効にならない | リクエストに合致するキーワードを使って、説明をより具体的にしてください。 |
| スラッシュコマンドが見つかりません | スキルフォルダー名が、入力した内容と一致しているか確認してください。スキルには、フロントマターを含む有効な SKILL.md ファイルが必要です |
| スキルが見つかりません | SKILL.md ファイルが正しい場所に存在し、有効なフロントマターが含まれているか確認してください |
| カスタムエージェントにスキルが不足しています | エージェントの ``resources`` フィールドに ``skill://`` の URI を追加してください |
| 誤ったスキルが起動されています | より具体的なキーワードを使用して説明を区別してください |

## 関連ドキュメント

- [ステアリング](https://kiro.dev/docs/steering/) - プロジェクト固有のコンテキストと標準
- [Powers](https://kiro.dev/docs/powers/) - バンドルされたナレッジとのMCP統合
- [カスタムエージェント](https://kiro.dev/docs/custom-agents/) - エージェントの設定とリソース
- [エージェントスキルの仕様](https://agentskills.io/specification) - フォーマットの詳細


---

[← 前へ: Troubleshooting](custom-agents/troubleshooting.md) | [↑ 親ページ](../../../index.md) | [次へ →: Powers](powers/index.md)
