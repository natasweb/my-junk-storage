# Plan mode

> 元URL: https://kiro.dev/docs/specs/plan/  
> 最終取り込み日: 2026-09-05  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

「プランモード」は、行動に移す前に考えを整理するのに役立ちます。体系的な要件収集とコードベースの調査をガイドし、実行段階へと引き継ぐ実装計画を生成します。完全な機能仕様書とは異なり、プランモードでは正式な要件書・設計書・タスク文書は生成されません。体系的な出力を提供しつつも、より迅速で、会話のような自然な流れで進めることができます。

| 機能 | IDE | CLI | Web | モバイル |
| --- | --- | --- | --- | --- |
| プランモード | ✓ | ✓ | — | — |

## プランモードの使用タイミング

- コードを書く前にアプローチをじっくり考えたいような複雑なタスク
- 既存のアーキテクチャを理解することが重要な、複数のファイルにわたる変更
- 通常、「まずはこれを理解してから実装しよう」と言うような状況

正式な要件定義書や設計書が必要な、明確に定義された機能については、代わりに[「機能仕様書」](https://kiro.dev/docs/specs/feature-specs/)を使用してください。迅速かつ内容が十分に理解されている変更については、計画段階を省略し、デフォルトのエージェントを直接使用して作業を進めてください。

## 呼び出し方法

新しいセッションを開始する際、**「Let's build」**画面のワークフローオプションから**「Plan」**を選択します。セッション途中で切り替えるには、チャット入力画面の下部バーにあるエージェント名をクリックし、エージェントピッカーから**「Plan」**を選択します。

チャット入力欄のエージェント名は、どのエージェントがアクティブであるかを示しています。

## ワークフロー

### 1. 要件の収集

プランナーが構造化された質問を行い、初期のアイデアを具体化します：

bash

```bash
[plan] > I want to build a todo app

I understand you want to build a todo app. Let me help you plan this.

[1]: What platform should this target?
a. Web Application
b. Mobile App
c. CLI Tool
d. Other

[2]: What's the primary use case?
a. Personal Task Management
b. Team Collaboration
c. Project Management
d. Other

(Answer with "1=a, 2=b" or provide your own answers)
```

個々の質問に回答したり、自由形式で回答したり、質問を完全にスキップしたりすることができます。

### 2. 調査と分析

プランナーは、読み取り専用モードでコードベースを調査します：

- ファイルを読み込み、コードインテリジェンスを活用して既存のアーキテクチャを把握します
- grep や glob を使用して関連するパターンを検索します
- Web検索を通じて技術を調査します
- 既存の規約に合わせて計画を調整します

### 3. 実装計画

明確な目標を盛り込んだ段階的な計画を作成します：

bash

```bash
**Implementation Plan - Todo CLI Command**

**Problem Statement:** Add todo management to existing application.

**Requirements:**
- CRUD operations for todos
- Local SQLite storage
- Priority and due date support

**Task Breakdown:**

Task 1: Create database schema and models
- Define Todo struct with required fields
- Create database migration
- Demo: Can create and query todos

Task 2: Implement command structure
- Add subcommand with add/list/complete operations
- Demo: CLI accepts todo commands

Task 3: Add advanced features
- Implement due dates and priority sorting
- Demo: Complete system with all features
```

### 4. 実行への引き継ぎ

計画が承認されると、自動的に実行が開始されます：

1. プランモードが終了し、エージェントは実行モードに移行します
2. 完成した計画がコンテキストとして実行エージェントに渡されます
3. 承認された計画を指針として、直ちに実装が開始されます

## 読み取り専用アクセス

プランモードでは、意図的にプロジェクトを変更することはできません。これにより、思考と計画立案に集中できるようになります：

| 操作 | 利用可能 |
| --- | --- |
| ファイルの読み取り | ✓ |
| コードインテリジェンス（シンボル、定義） | ✓ |
| 検索（grep、glob） | ✓ |
| Web検索 | ✓ |
| ファイルへの書き込み | — |
| コマンドの実行 | — |
| MCPツール | — |

## プランモードと仕様書

|  | プランモード | 機能仕様 |
| --- | --- | --- |
| **出力** | タスクの細分化を含む対話型計画 | Formal requirements.md、design.md、tasks.md |
| **プロセス** | 対話型Q&A → 計画 → 引き継ぎ | 要件 → 設計 → 承認ゲートを設けたタスク |
| **スピード** | 所要時間 | より長い（レビューを伴う多段階） |
| **最適** | 中程度の複雑さ、「探索してから構築」 | 複雑度が高い、チームでの共同作業、監査証跡 |
| **成果物** | 計画は会話の文脈に存在します | `.kiro/specs/`に永続化 |

## ヒント

- 15～60分の実施時間を要するタスクには「プラン」モードを使用してください。計画の恩恵を受けられるほど十分な長さであり、一方で正式な仕様書を作成するには過剰なほど短い時間です
- 構造化された質問に積極的に取り組む――質の高い入力ほど、より良い計画が生まれる
- プランナーにコードベースを調査させましょう。見落としがちなパターンを発見してくれます
- 引き継ぎを承認する前に計画を確認しましょう。期待通りに合致するまで、反復して調整してください


---

[← 前へ: Quick Spec](quick-spec.md) | [↑ 親ページ](index.md) | [次へ →: Analyze Requirements](analyze-requirements.md)
