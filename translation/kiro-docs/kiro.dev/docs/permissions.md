# Permissions

> 元URL: https://kiro.dev/docs/permissions/  
> 最終取り込み日: 2026-09-05  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

Kiroは、エージェントの動作をきめ細かく宣言的に制御できる、機能ベースの権限システムを採用しています。機能ごとに一致パターンと明示的な効果を定義することで、従来の二値的な信頼モデルに取って代わります。

| 機能 | IDE | CLI | Web | モバイル |
| --- | --- | --- | --- | --- |
| 権限YAML設定 | ✓ | ✓ | 該当なし | — |
| 対話型承認プロンプト | ✓ | ✓ | 該当なし | — |
| グローバルおよびワークスペースのスコープ | ✓ | ✓ | 該当なし | — |
| アクションごとのプロンプトなしのサンドボックス実行 | — | — | ✓ | — |

Web版では、`permissions.yaml`モデルおよび対話型承認は適用されません。エージェントはアクションごとの確認を行う代わりに、隔離された[クラウドサンドボックス](https://kiro.dev/docs/web/sandbox/)内で実行されるため、これらの行は「非対応」ではなく「該当なし」とマークされます。

## 仕組み

権限システムは、3つの中核となる概念に基づいて構築されています：

| 概念 | 説明 |
| --- | --- |
| **機能** | `fs_read`、`fs_write`、`shell`、`web_fetch`、`web_search`、`mcp`、`subagent`、`skill`、`power`、`context`、`diagnostics`、`sandbox_network`。メタ機能の詳細：`all`（すべて）、`builtin`（すべての組み込みツール）、`filesystem`（`fs_read` + `fs_write`） |
| **効果** | `deny` (常にブロック)、`ask` (プロンプトを表示)、`allow` (黙って処理を続行) |
| **優先順位** | deny > ask > allow - スコープにかかわらず、denyルールが常に優先される |

追加のルールプロパティ：

| プロパティ | 説明 |
| --- | --- |
| **一致パターン** | ルールの適用範囲を指定するグロブパターン（fsの場合はファイルパス、shellの場合はコマンドのプレフィックス、MCPの場合はサーバー名またはツール名） |
| **除外** | 一致させてはならないオプションのグロブパターン — 「X 以外のすべてを許可」を有効にします |

## ルールの定義

権限は、YAML ファイル内で 2 つのレベルで定義されます：

**ユーザースコープ**（`~/.kiro/settings/permissions.yaml`） - すべてのプロジェクトに適用されます。信頼できる操作を事前に承認するために使用します：

yaml

```yaml
rules:
  - capability: shell
    match: ["git *", "npm *", "npx *"]
    effect: allow

  - capability: fs_write
    match: ["src/**", "tests/**"]
    effect: allow

  - capability: fs_read
    effect: allow

  - capability: mcp
    match: ["my-server/*"]
    effect: allow
```

**ワークスペーススコープ**（`~/.kiro/workspace-roots/<hash>/permissions.yaml`） - 特定のプロジェクトにのみ適用されます。ルールを1つのコードベースに限定する場合に使用します：

yaml

```yaml
rules:
  - capability: fs_write
    match: ["*.env", "*.pem", "*.key"]
    effect: deny

  - capability: shell
    match: ["rm -rf *", "sudo *"]
    effect: deny
```

どちらのスコープも、すべてのエフェクト（`deny`、`ask`、`allow`）をサポートしています。

### 除外構文

ルールでは、「～を除くすべてを許可する」パターン用の `exclude` フィールドがサポートされています：

yaml

```yaml
rules:
  - capability: mcp
    match: ["my-server/*"]
    exclude: ["my-server/dangerous-tool"]
    effect: allow
```

### パターンマッチング

ルールではグロブパターンが使用されます。構文は機能の種類によって異なります：

**ファイルシステムパターン**（`fs_read`、`fs_write`）：

- `*` 単一のパス構成要素内での一致
- `**` パス区切り文字をまたいで一致
- `{a,b}` 中括弧展開および`[abc]`の文字クラスがサポートされています
- ワイルドカードを含まないパターンは、暗黙的に子要素と一致します。`~/temp` は `~/temp/child` と一致します

**シェル、Web、および MCP パターン：**

- `*` 任意の文字列に一致する
- `**`、`?`、および文字クラスはサポートされていません

yaml

```yaml
rules:
  # Allow npm commands except npm publish
  - capability: shell
    effect: allow
    match:
      - "npm *"
    exclude:
      - "npm publish*"

  # Deny reads to secrets at any depth
  - capability: fs_read
    effect: deny
    match:
      - "**/.env"
      - "**/.env.*"
      - "secrets/**"
      - "**/*.pem"
```

### シェルコマンドの解析

シェルコマンドは、パターンマッチングの前に解析されます。複合コマンド（`;`、`&&`、`||`、`|`を使用するもの）は分割され、各サブコマンドが個別に評価されます。これにより、`npm test *`というルールが、誤って`npm test ; curl attacker.com`に一致してしまうのを防ぎます。

## スコープ

権限は複数のスコープにわたって評価されます。

| スコープ | 場所 | 許可される効果 |
| --- | --- | --- |
| Kiro | ハードコードされたセキュリティ不変条件（設定によって変更できないもの） | 拒否、確認 |
| 管理 | `managed-settings.json` における[エンタープライズ権限ポリシー](https://kiro.dev/docs/enterprise/governance/permissions/) | 拒否、確認 |
| ユーザー | `~/.kiro/settings/permissions.yaml` | 拒否、確認、許可 |
| ワークスペース | `~/.kiro/workspace-roots/<hash>/permissions.yaml` | 拒否、確認、許可 |
| エージェント | エージェントプロファイルに組み込まれている（`permissions`フィールド） | 拒否、確認、許可 |
| セッション | セッション中の同意決定に基づくインメモリ・ルール | 拒否、確認、許可 |

ルールは、「拒否が優先される」アルゴリズム（deny > ask > allow）を使用して評価されます。スコープ間の優先順位はなく、どのスコープに由来するかにかかわらず、最も制限の厳しい効果が優先されます。

## デフォルトの動作

`permissions.yaml`が設定されていない場合、デフォルトのエージェントポリシーでは以下が許可されます：

- `fs_read` `./**` では、ワークスペース内の任意のファイルを通知なしで読み取り可能
- `shell` 一般的なGitの読み取り専用コマンド（`git status`、`git log`、`git diff`、`git branch`など）については
- `shell` システム情報コマンド用 - `pwd`、`whoami`、`uname` など
- ユーティリティツール（診断、ナレッジなど）

Kiroの適用範囲（ハードコードされており、設定で変更不可）では以下を強制します：

- **常に拒否：**`~/.kiro/settings/`、`.kiro/settings/`、および `~/.kiro/workspace-roots/` への書き込み（エージェントが自身の権限ファイルを変更できないようにするため）
- **常に確認を求める：**`.git/**`、`.kiro/agents/**`、`.kiro/hooks/**`、`.kiroignore` への書き込み

それ以外はすべて承認を求めます。`permissions.yaml` を作成すると、これらのデフォルト設定に追加され、置き換えられることはありません。

## インターフェースごとの権限管理

### エージェントの自律性設定

`permissions.yaml`のルールに加え、IDEのエージェントの自律性は [**設定] → [エージェント] → [エージェントの自律性**]（設定キー：`kiroAgent.agentAutonomy`）で制御されます。モードは以下の2つです：

- **オートパイロット** - エージェントは、確認を求めずに許可された操作を実行します
- **監督モード** - エージェントは、いかなるアクションを実行する前にも確認を求めます

機能ベースの権限レイヤーは、自律モードによって実行の可否が決定された後に適用されます。これら2つのレイヤーを組み合わせることで、大まかな制御（オートパイロット対スーパーバイズド）に加え、特定の機能に対するきめ細かなルール（permissions.yaml）を実現できます。

### 対話型承認フロー

ツールが承認を必要とする場合、チャットにプロンプトが表示されます。現在の呼び出しに対して、**「許可」**と**「拒否」**の選択肢が利用可能です。また、Kiroは、要求されたコマンドに適用可能な保存済みルールを導き出せる場合、永続的な選択肢も表示します：

| アクション | 効果 |
| --- | --- |
| **許可** | この特定の実行を1回承認する |
| **常に許可** | 永続的な許可ルールを作成する（パターン／スコープピッカーが開きます） |
| **拒否** | この特定の呼び出しを1回だけブロックする |
| **常に拒否する** | 永続的な拒否ルールを作成する |

Kiro が保存済みのルールの有効性を確認できない場合、「**常に許可」**および「**常に拒否」**のオプションは非表示になり、その理由がプロンプトに説明されます。

「**常に許可」**を選択すると、次の 2 つの設定を行います。

- **パターン** - ルールの照合範囲を選択します（例：任意の cd コマンドに対して「`cd *`」、または正確なコマンドパス）
- **適用先** - ルールを保存する場所を選択します：
  - **すべてのワークスペース** — ユーザースコープの `~/.kiro/settings/permissions.yaml` に保存
  - **このワークスペース** — `~/.kiro/workspace-roots/<hash>/permissions.yaml` に保存されます（ユーザー単位、リポジトリ外）
  - **このセッション** — セッションが終了するまでメモリ内に保持されます

「パターン」ドロップダウンには、特定の操作を一般化したバージョンが候補として表示されます。たとえば、正確なコマンド ``git add contents/docs/`` はパターン ``git add *`` となり、正確なパス ``.env.local`` は ``.env*`` または ``**/.env*`` となります。この候補を編集して、より制限を厳しくしたり、より許容範囲を広げたりすることができます。

連鎖したコマンド（例：`cd /path && cargo build`）の場合、連鎖内の各サブコマンドは個別に承認を求める形で提示されます。

## 権限設定の例

以下に、権限設定の一般的なパターンを示します：

| シナリオ | 設定 |
| --- | --- |
| ファイルの読み取りを許可 | `capability: fs_read`, `effect: allow` |
| プロジェクトディレクトリへの書き込みを許可する | `capability: fs_write`, `match: ["src/**", "tests/**"]`, `effect: allow` |
| 機密ファイルへのアクセスをブロックする | `capability: fs_write`, `match: ["*.env", "*.pem", "*.key"]`, `effect: deny` |
| 危険なコマンドをブロック | `capability: shell`, `match: ["rm -rf *", "sudo *"]`, `effect: deny` |
| 特定のMCPサーバーを許可 | `capability: mcp`, `match: ["my-server/*"]`, `effect: allow` |
| 本番環境ではシェルを信頼しない | `capability: shell`、`effect: ask`（またはCLIで`/tools untrust shell`を使用） |

## 旧バージョンからの移行

CLI 2.x または IDE 0.x からアップグレードする場合は、以前の権限の仕組みや変更点について、リファレンスページを参照してください:

- [CLI 2.x リファレンス - 権限](https://kiro.dev/docs/cli/2x-reference/#permissions-and-tool-trust)
- [IDE 0.x リファレンス - 権限](https://kiro.dev/docs/ide/0x-reference/#permissions-autopilot--supervised-toggle)
- [CLI 3.0の新機能 - 権限の移行](https://kiro.dev/docs/cli/v3/permissions/)


---

[← 前へ: Registry (enterprise)](mcp/registry.md) | [↑ 親ページ](../../../index.md) | [次へ →: Custom agents](custom-agents/index.md)
