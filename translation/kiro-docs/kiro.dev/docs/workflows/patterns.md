# Workflow examples

> 元URL: https://kiro.dev/docs/workflows/patterns/  
> 最終取り込み日: 2026-10-06  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

このページに掲載されている例は、あくまで出発点です。バンドルされたレシピは、有用な形の一例を示すものであり、バンドルされたレシピのセットはクライアントのバージョンによって異なる場合があります。作業内容に合わせて、手順、エージェント、モデル、工数、制限、引き継ぎ、および完了の証拠を変更してください。

| 例 | 形状 | 完了の証拠 |
| --- | --- | --- |
| [調査と説明](https://kiro.dev/docs/workflows/patterns/#investigate-and-explain) | 1つの重点的なステップ → レポート → メインエージェントによる統合 | 引用された報告書の成果物 |
| [機能の提供](https://kiro.dev/docs/workflows/patterns/#deliver-a-feature) | 要件 → 設計／レビュー → 計画 → コーディング／レビュー → 検証 | 承認されたレビューと最終検証 |
| [検証されるまでトラブルシューティング](https://kiro.dev/docs/workflows/patterns/#troubleshoot-until-verified) | 再現 → 診断 → 修正 → テスト ↺ | 機械可読な合格結果 |
| [公開と対応](https://kiro.dev/docs/workflows/patterns/#publish-and-respond) | 公開 → 待機 → 対応 ↺ → 引き継ぎ | 外部PRの状態とチェック |

## 調査と説明

ソースコードの変更を伴わない、完結した回答が必要な場合にこれを使用してください。親の会話から開始してください：

ワークフローを使用して、このリポジトリのデプロイメントアーキテクチャを調査してください。プロジェクトを変更しないでください。エントリポイント、依存関係、リスク、テスト、および変更の可能性が高い箇所を網羅した、出典を明記したレポートを作成し、重要な調査結果をここに要約してください。

このレシピは意図的に簡潔にしています：

```text
brief → investigate · wf-planner → report artifact → main-agent summary
```

再利用可能な最小限のレシピ：

yaml

```yaml
name: investigate-question
inputs:
  brief: prompt
  report_path: file
steps:
  - type: step
    id: investigate
    agent: wf-planner
    artifacts:
      report: "{{report_path}}"
    prompt: >
      READ-ONLY investigation; do not edit code or commit.
      Investigate {{brief}} and write findings to {{report_path}}.
      Lead with the answer, cite files and symbols, list risks and recommendations,
      then call send_message with severity success and the key finding.
```

ステップを1つにまとめることで、実行コストを低く抑え、監査性を確保します。`report`を宣言することで、ワークフローが拡大した場合でも、後続のステップで`{{artifacts.report}}`としてそのパスを利用できるようになります。

**障害防止策：**実行ワークスペース内のパスを使用し、読み取り専用という制約を明示的に指定します。ワークフロー自体ではエージェントを読み取り専用にはできません。選択されたエージェントツールと権限が、依然として副作用を制御します。

## 機能の提供

要件定義、設計、実装計画、コーディング、独立レビュー、最終検証を、単一の長い議論ではなく、明確な段階として進める必要がある場合にこれを使用します。これは、ほとんどの機能開発における典型的な流れです：

1. **要件定義。**1人のエージェントが、自身のセッション内でタスクをテスト可能な成果物に変換するため、誰かがそれに基づいて設計を行う前に、制約条件がファイルとして存在することになります。
2. **設計、レビュー済み。**設計者が草案を作成し、独立したレビュー担当者が評価を行う。このループは、承認または所定の回数制限に達した時点で終了する。レビュー担当者は設計者の思考過程を見ることはなく、設計内容のみを確認する。
3. **計画。**プランナーが、承認された設計を順序立てられた実装計画に変換します。
4. **実行、レビュー。**コーダーが計画を実装し、異なるモデルを持つ2人のレビューアが並行して結果をレビューし、アグリゲーターがその結果を統合して1つの評価を下します。承認されるまでこのループを繰り返します。
5. **検証。**最終ステップでは、完成した成果物を計画ではなく、元の要件と照らし合わせてチェックします。

この図は、バンドルされた`feature-pipeline`のトポロジーを再現したものです。これには、名前付きエージェント、モデルオーバーライドを持つ2人のレビューア、`code-aggregate`、およびトップレベルステップとしての`validate`が含まれます。 以下に示す骨格は、これを基に、明示的なワークツリーの分離と機械可読な検証ゲートを追加して改変したものです。これは、レシピそのものとしてではなく、リポジトリのポリシー策定の出発点としてご利用ください。

図 2.2: バンドルされた機能パイプライン · エージェント、モデル、およびループ

1/6·セットアップ

一時停止中。6 ステップ中のステップ 1。

トポロジー全体が表示されます。ノードを選択するか、トランスポートを使用して、セットアップから検証までの実行経路を追跡してください。

- ステップ
- コンテナ
- ゲート

ステップ暗黙のシーケンス

設計草案

設計レビュー

◇ 承認済み？

↺ 設計草案へ修正

実装

レビュー・ファブル

レビュー・GPT

コード集約

◇ 承認済み？

↺ 「実装」に修正

Graph

1setup

隔離されたワークツリーを確認し、一意の実行ディレクトリを作成する。

レシピ・選択されたセットアップ

```
- type: step
  id: setup
  agent: wf‑coder
```

ハンドオフ→ワークツリーパス→実行ID → 実行ディレクトリ

実線の四角形はエージェントステップ、破線の境界線は繰り返しノードおよび並列ノード、ひし形は繰り返しノードがチェックする条件を表します。2人のレビューアは意図的に異なるモデルで実行されます

同梱のサンプルは、Opus 5 で「高」の労力レベルで起動し、その後、効果のある箇所で設定を上書きします。具体的には、設計およびレビューエージェントは「極めて高」の労力レベルで実行され、2人のコードレビュー担当者は意図的に異なるモデル（`claude-fable-5` および `gpt-5.6-sol`）を使用することで、その発見が単なる名称上の違いにとどまらず真に独立したものとなるよう配慮されています。また、1行の「`setup`」ステップは「低」の労力レベルで実行されます。残りのステップは、起動モデルと労力レベルを継承します。

- `design-loop`：設計／レビューの試行は最大3回まで。`design-review.json`が「`APPROVED`」と判定した場合にのみ停止し、条件を満たさない場合は中止する。
- `code-loop`：実装／レビューの試行は最大3回。両方のレビューを並行して実行し、その結果を`code-review.json`に集約し、その判定が「`APPROVED`」となった場合にのみ停止する。条件を満たさない場合は中止する。
- `validate`: 元の要件に対する最終チェックを1回実行する。

以下は、適応可能なスケルトンです。これは同じステージを維持しつつ、バンドルされたレシピではユーザーに任されていた2つの要素を追加しています。それは、実行ごとに隔離されたワークツリーと、最終チェックを機械可読な`PASS`またはabortに変換する`validate-gate`の繰り返しです。

yaml

```yaml
name: deliver-feature
inputs:
  task: prompt
  worktree_path: string
  run_id: string
steps:
  - type: step
    id: setup
    agent: wf-coder
    prompt: >
      Verify that {{worktree_path}} is an absolute path to an isolated worktree.
      Create {{worktree_path}}/.kiro/workflow-runs/{{run_id}} for this run.
      Do not change the primary checkout. Report the selected path.

  - type: step
    id: requirements
    agent: wf-design
    artifacts:
      requirements: "{{worktree_path}}/.kiro/workflow-runs/{{run_id}}/requirements.md"
    prompt: >
      In {{worktree_path}}, write testable requirements for {{task}} to
      {{worktree_path}}/.kiro/workflow-runs/{{run_id}}/requirements.md.

  - type: repeat
    id: design-loop
    maxIterations: 3
    onMaxIterations: abort
    stopCondition:
      fileCheck:
        path: "{{worktree_path}}/.kiro/workflow-runs/{{run_id}}/design-review.json"
        jsonPath: verdict
        value: APPROVED
    steps:
      - type: step
        id: design-draft
        agent: wf-design
        artifacts:
          design: "{{worktree_path}}/.kiro/workflow-runs/{{run_id}}/design.md"
        prompt: >
          In {{worktree_path}}, read {{artifacts.requirements}}.
          Draft or revise the design and write it to {{worktree_path}}/.kiro/workflow-runs/{{run_id}}/design.md.
      - type: step
        id: design-review
        agent: wf-design-reviewer
        artifacts:
          design_review: "{{worktree_path}}/.kiro/workflow-runs/{{run_id}}/design-review.json"
        prompt: >
          In {{worktree_path}}, review {{artifacts.design}} against
          {{artifacts.requirements}}. Write {{worktree_path}}/.kiro/workflow-runs/{{run_id}}/design-review.json
          with verdict APPROVED or REVISE and the findings.

  - type: step
    id: plan
    agent: wf-planner
    artifacts:
      plan: "{{worktree_path}}/.kiro/workflow-runs/{{run_id}}/plan.md"
    prompt: >
      In {{worktree_path}}, write an ordered implementation and validation plan
      from {{artifacts.requirements}} and {{artifacts.design}} to
      {{worktree_path}}/.kiro/workflow-runs/{{run_id}}/plan.md.

  - type: repeat
    id: code-loop
    maxIterations: 3
    onMaxIterations: abort
    stopCondition:
      fileCheck:
        path: "{{worktree_path}}/.kiro/workflow-runs/{{run_id}}/code-review.json"
        jsonPath: verdict
        value: APPROVED
    steps:
      - type: step
        id: implement
        agent: wf-coder
        artifacts:
          code_summary: "{{worktree_path}}/.kiro/workflow-runs/{{run_id}}/code-summary.md"
        prompt: >
          Implement {{artifacts.plan}} in {{worktree_path}} and run relevant checks.
          Write {{worktree_path}}/.kiro/workflow-runs/{{run_id}}/code-summary.md with changed files, tests run,
          command output, and remaining risks.
      - type: parallel
        id: code-reviews
        joinPolicy: all
        branches:
          - type: step
            id: review-a
            agent: semantic_reviewer
            artifacts:
              review_a: "{{worktree_path}}/.kiro/workflow-runs/{{run_id}}/review-a.md"
            prompt: >
              Independently review the implementation in {{worktree_path}} using
              {{artifacts.code_summary}}. Write findings to {{worktree_path}}/.kiro/workflow-runs/{{run_id}}/review-a.md.
              Do not read other reviews.
          - type: step
            id: review-b
            agent: semantic_reviewer
            artifacts:
              review_b: "{{worktree_path}}/.kiro/workflow-runs/{{run_id}}/review-b.md"
            prompt: >
              Independently review the implementation in {{worktree_path}} using
              {{artifacts.code_summary}}. Write findings to {{worktree_path}}/.kiro/workflow-runs/{{run_id}}/review-b.md.
              Do not read other reviews.
      - type: step
        id: code-aggregate
        agent: wf-review-aggregator
        artifacts:
          code_review: "{{worktree_path}}/.kiro/workflow-runs/{{run_id}}/code-review.json"
        prompt: >
          In {{worktree_path}}, read {{artifacts.review_a}} and
          {{artifacts.review_b}}. Write {{worktree_path}}/.kiro/workflow-runs/{{run_id}}/code-review.json with
          verdict APPROVED or REVISE, deduplicated findings, and corroborated
          issues ranked first.

  - type: repeat
    id: validate-gate
    maxIterations: 1
    onMaxIterations: abort
    stopCondition:
      fileCheck:
        path: "{{worktree_path}}/.kiro/workflow-runs/{{run_id}}/validation.json"
        jsonPath: verdict
        value: PASS
    steps:
      - type: step
        id: validate
        agent: wf-coder
        artifacts:
          validation_report: "{{worktree_path}}/.kiro/workflow-runs/{{run_id}}/validation.md"
          validation: "{{worktree_path}}/.kiro/workflow-runs/{{run_id}}/validation.json"
        prompt: >
          In {{worktree_path}}, validate the implementation against
          {{artifacts.requirements}} and {{artifacts.code_summary}}.
          Run the required checks. Write {{worktree_path}}/.kiro/workflow-runs/{{run_id}}/validation.md and
          {{worktree_path}}/.kiro/workflow-runs/{{run_id}}/validation.json with verdict PASS or FAIL and the evidence.
          Do not fix code in this step; report honestly.
```

ワークツリーの選択と実行の設定は明示的であり、ワークフローの自動動作ではありません。`worktree_path`を許可されたワークスペースのルート内に保持してください。つまり、隔離されたワークツリー自体からopenまたはlaunchを実行するか、現在のワークスペース内にワークツリーを作成してください。このレシピ内のすべてのアーティファクトおよび`fileCheck`パスは`worktree_path`配下に存在し、許可されたワークスペースのルート外にある`fileCheck`パスは、そのノードの検証に失敗します。 同時実行時には、`run_id`の値をそれぞれ異なる値に設定し、アーティファクトやゲートが衝突しないようにしてください。この例では変更の妥当性は検証されますが、リポジトリの制御はバイパスされません。これを反映させるには、検証済みのブランチを[Publish and Respond](https://kiro.dev/docs/workflows/patterns/#publish-and-respond)に提出してレビューを受け、プルリクエストが承認されてグリーン状態になったら、通常のリポジトリプロセスを通じてマージしてください。

**失敗時の安全策：**反復回数の上限は時間と使用量を制限するものであり、品質を保証するものではありません。この例では、レビューの承認または最終検証の結果のいずれかが満たされない場合、黙って処理を進めるのではなく、処理が中止されます。

## 検証されるまでトラブルシューティングを行う

これは、ビルドの失敗、テストの失敗、デプロイの検証、パフォーマンスの退行など、再現可能で機械的に確認できる失敗に適用します。

この失敗を再現するために範囲を限定したワークフローを使用し、原因を1つずつ診断し、最小限の修正を適用して、同じチェックを再実行します。`result.json`が「`verified: true`」を記録した時点で停止し、10回の失敗後に一時停止します。

各反復では、障害を再現し、1つの原因を特定し、最小限の変更を適用し、同じチェックを再実行します。このループは、`result.json`が判定結果を記録し、capが成功を宣言する代わりに実行を一時停止した時点で終了します。このスケルトンでは、これらの処理を単一のステップにまとめています。各処理に独自のエージェントや証拠ファイルが必要な場合は、それらを個別のステップに分割してください。

yaml

```yaml
name: troubleshoot-until-verified
inputs:
  issue: prompt
  result_path: file
steps:
  - type: repeat
    id: fix-loop
    maxIterations: 10
    onMaxIterations: pause
    stopCondition:
      fileCheck:
        path: "{{result_path}}"
        jsonPath: verified
        value: true
    steps:
      - type: step
        id: diagnose-and-fix
        agent: wf-coder
        prompt: >
          Reproduce {{issue}}. Inspect the latest result at {{result_path}} if it exists.
          Test one hypothesis, apply the smallest justified change, rerun the reproduction,
          and write JSON with verified, evidence, and remaining_risk fields.
```

各反復では、リポジトリと同一のチェックから真値を再導出します。`fileCheck`はランタイムに強制可能な停止条件を提供します。文章による成功の主張だけでは、ループは終了しません。

**失敗時のガードレール：**`onMaxIterations: pause` は、無期限に処理を続けたり、誤った成功を報告したりする代わりに、決定結果を明示します。

## 公開と対応

ブランチがレビューの準備が整った後にこれを使用します。ウォッチはモデルのターンなしで待機し、フォーカスされたレスポンダーが新しいアクティビティに対応し、プルリクエストが終端状態に達するとループは終了します。終端状態とは、あなたまたはリポジトリがマージを実行するか、プルリクエストをクローズしたときを指します。ワークフローの成果物は、承認済みの「グリーン」なプルリクエストであり、マージはあなた次第です。

下の図は、このループをループとして描いています。パーティクルは「watch」が停止している間は静止し、アクティビティが発生すると「`respond`」を1周し、プルリクエストをマージまたはクローズした時点で初めて出口から出ていきます。

図 2.4: 公開・待機・応答・完了

1/4・保留中

一時停止中。4ステップ中のステップ1。

submit
:   PR #418 が開かれました

respond
:   イベントごとに1ターン

待機
:   保留中 · モデルターン 0回

ターミナル
:   停止条件: wait.terminal

01submitがプルリクエストを開き、waitはそのプルリクエストで保留状態になっています。重要なのは「空のリング」という点です。何も起こらない間は、何も消費されません。

イベントの間、ループは保留状態になります：モデルのターンもコストも発生しません。すべてのイベントは、`respond`の1ターンに相当します。

リングは繰り返しを表します。パーティクルはイベントが発生するまでリング上に留まり、イベントが発生すると「respond」を経由して1周し、「wait」を通って戻ってきます。ターミナルイベントのみが分岐経路を通じてリングを離れます

yaml

```yaml
name: publish-and-respond
inputs:
  branch: string
  run_dir: string
steps:
  - type: step
    id: submit
    agent: wf-pr-submitter
    prompt: >
      Publish {{branch}} as a pull request, or reuse its open PR.
      Write its URL and metadata to {{run_dir}}/pr.json.
  - type: repeat
    id: pr-loop
    maxIterations: 200
    onMaxIterations: pause
    stopWhen: wait.terminal
    steps:
      - type: watch
        id: wait
        handler: github-pr
        config:
          prRef: "{{run_dir}}/pr.json"
      - type: step
        id: respond
        agent: wf-pr-responder
        prompt: >
          Respond to the new PR activity in {{wait.output}}.
          Address feedback that is safe and in scope. Ask before changing the design,
          expanding scope, or altering the user experience.
```

レスポンダーは、通常のウォッチアクティビティと終端イベントの両方の後に実行されるため、プルリクエストがマージまたはクローズされた後に最終的な後処理を行うことができます。このワークフローは、認証情報、ブランチ保護、必須チェック、承認ポリシーを上書きすることはなく、マージも行いません。承認済みの「グリーン」なプルリクエストをユーザーに渡し、ユーザーまたはリポジトリの処理がマージを実行します。

**失敗防止策：**同時実行されるプロセスには個別の`run_dir`値を割り当て、`pr.json`を共有しないようにし、GitHubの認証情報については最小権限の原則に従ってください。

この「待機と応答」のループは、スクリプトでチェック可能なあらゆる対象に適用できます。`github-pr`のウォッチを、独自のプログラムを実行する`command`のウォッチに置き換えることで、ループは別のシステムにおけるチケット、デプロイ、またはビルドを待機するようになります。詳細は[「`command`で他のあらゆるものを監視する](https://kiro.dev/docs/workflows/authoring/#watch-anything-else-with-command)」を参照してください。

## オーケストレーションを試してみる

ワークフローの構造は、選択したモデルと同様にアウトプットの品質に影響を与える可能性があり、最適な構造はモデルによって異なります。計画がしっかりしたモデルでは、レビューのループ回数が少なくて済む場合があります。高速で低コストなモデルでは、より強力なレビュー担当者と低い上限が必要になる場合があります。1回のパスで深く推論を行うモデルは、短い間隔での反復には適さない可能性があります。 特定の構成が最善であると決めつけるのではなく、モデル、プロバイダー、労力、分解、批評、検証を実験の入力として扱い、反復を行うことを想定してください。

同梱の「`feature-pipeline`」レシピは、実証済みの解決策の一つです。これは、判断が重要な部分（設計とレビューを「Extra high」に設定）に労力を割き、2人のコードレビュー担当者を異なるモデルに割り当てることで独立性を確保しています。モデルを変更すれば、労力を割くべき適切な場所もそれに応じて変化します。

有用な実験例：

1. 1つのタスクセットと1つの評価基準を定義します。
2. 利用可能な複数の構成の下で、ステップごとの`modelId`および`effortLevel`のオーバーライドを使用して、同じ小規模なワークフローを実行します。
3. 各レーンを分離し、ある出力が別の出力に偏りをもたらさないようにする。
4. 品質、所要時間、推定使用量を成果物として記録する。
5. 宣言された評価器を使用して結果を比較する。
6. 根拠が示された場合は、ワークフローまたは構成を修正し、繰り返し実行します。

プロバイダーとモデルの利用可能性は、アカウントおよびクライアントによって異なります。実行前に、すべての `modelId` を検証してください。Kiro はプロバイダーを自動的に選択したり、プロバイダー間でフォールバックを行ったり、パフォーマンスの順序を保証したりすることはありません。評価基準に基づいて決定されます。

### 独立した判断のためのスキャッター・ギャザー

複数の独立した判断を1つの結果に収束させる必要がある場合は、`parallel`を使用します。多様性が有用な場合は、各分岐に異なる利用可能な構成を割り当て、その後、集約ステップで発見事項の重複を排除し、裏付けられた問題をランク付けします。現在、並列処理はコンテキストの分離と結合のセマンティクスを提供していますが、実処理時間が短縮されるとは想定しないでください。

### 範囲限定探索

結果が定まらない最適化については、反復ごとに1つの測定済み実験を実行してください。改善が見られた場合はそれを確定し、退行が見られた場合は元に戻してください。自然な正しさの条件が存在しない場合は、`maxIterations`を明示的なコスト上限として使用し、上限に達した時点で一時停止して人間の判断を仰いでください。

## これらを組み合わせる

上記の例は断片に過ぎません。実際の作業ではこれらが連鎖して行われ、最初からレシピを書くことはめったにありません。親チャットで成果物を説明すると、Kiroがワークフローを提案するか、あるいはバンドルされたレシピに名前を付けて入力を渡すことになります。 3つの典型的なチェーンがあり、それぞれKiroの役割が終わる地点で終了します。機能提供のための承認済み（グリーン）プルリクエスト、ローカル修正のための検証済みブランチ、または読み取り専用作業のための引用済みレポートです。マージはすべて、あなた自身またはあなたのリポジトリが行います。

**チケットから機能をリリースします。**Kiroに、新しいワークツリーで変更をデリバリーするよう依頼します。Kiroはワークツリーをセットアップし、要件を記述し、設計とレビューを繰り返して、計画を立て、実装と2モデルによるレビューを繰り返して、要件に対して検証を行い、その後プルリクエストを発行して、それをウォッチリストに登録します。 実行中は親チャットで作業を続けます。レビューアがコメントすると、レスポンダーが起動し、安全な対応を行い、スコープを変更する前に確認を求めます。満足したらマージします。

**ビルドの失敗を修正します。**Kiroに、動作を変更せずにビルドを成功させるよう依頼します。Kiroは失敗を再現し、診断、最小限の変更、同じチェックが通過するまで再実行というループを繰り返します。この際、リソースを浪費するのではなく、あなたが対応できるよう一時停止する上限が設定されています。 原因が設計上の問題であることが判明した場合、そのステップは一時停止して確認を求めます。あなたはそのステップのセッション内、またはメインエージェントを通じて回答し、履歴を保持したままループが継続します。最終的に、修正がブランチに反映され、チェックに合格したことを証明するエビデンスファイルが生成されて終了します。

**変更を行う前に状況を把握しましょう。**コードを変更する前に、Kiroに不慣れな領域を調査させ、リスクを報告してもらいます。1つの読み取り専用ステップで引用付きのレポートが生成され、メインエージェントがチャットでその要約を表示します。これでこの実行は終了します。何も変更されていないため、ブランチもプルリクエストも発生せず、即座に行動に移せるレポートが得られるのです。 もしレポートに基づいて変更が必要だと判断された場合は、その旨を伝えてください。そうすれば、Kiroはレポートを最初の入力として上記の機能チェーンを提案します。これにより、要件定義のステップは、コードベースに関するあなたの記憶ではなく、証拠に基づいて開始されます。

チェーンを組む際は、各境界を明確に保ってください：

- 永続的なファイルはアーティファクトとして渡し、短い結論はキャプチャされた出力として渡してください。
- ループには、存在する場合、機械が読み取れる停止条件を指定してください。
- 反復回数の上限は、成功の証明ではなく、安全上の制限として扱ってください。
- ワークスペースとワークツリーのパスを明示的に保つ。
- 実行前に、エージェントツール、認証情報、および副作用を確認してください。

## 次のステップ

- [「ワークフローの作成」では](https://kiro.dev/docs/workflows/authoring/)、レシピスキーマ、ノードタイプ、テンプレート、停止条件、エージェント、モデル、および検証について包括的に説明しています。
- [「ワークフローの実行と管理」では、](https://kiro.dev/docs/workflows/manage/)クライアント全体にわたる監視、ステアリング、制御、およびリカバリについて説明しています。


---

[← 前へ: Author workflows](authoring.md) | [↑ 親ページ](index.md) | [次へ →: Run and manage workflows](manage.md)
