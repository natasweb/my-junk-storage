# Kiroignore

> 元URL: https://kiro.dev/docs/kiroignore/  
> 最終取り込み日: 2026-09-05  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

`.kiroignore`ファイルは、Kiroがワークスペース内の特定のファイルを読み取れないようにします。おなじみのgitignore構文を使用して、非公開にしておくべきファイル（認証情報、シークレット、またはエージェントのコンテキストから除外したいコンテンツなど）のパターンを定義します。

対応している場合、無視パターンはエージェントのツール全体に適用されます。つまり、どのツールが読み込もうとしても、ブロックされたファイルはブロックされたままになります。Kiroは、ファイルやテキストの検索結果を返す前に、各結果をユーザーの読み取り権限および無視ルールと照合します。無視されたファイルや読み取り不可能なファイルのパスおよび一致するコンテンツは、検索結果に表示されません。対応状況については、以下の表をご確認ください。

| 機能 | IDE | CLI | Web | モバイル |
| --- | --- | --- | --- | --- |
| ワークスペース`.kiroignore` | ✓ | V3検索 | — | — |
| グローバル無視ファイル (`~/.kiro/settings/kiroignore`) | ✓ | — | — | — |

IDEは、サポート対象のエージェントツール全体で`.kiroignore`を適用します。CLI V3では、コンテンツ検索およびファイル名検索の結果のフィルタリングにのみ対応しています。Webでのサポートはまだ利用できません。

## .kiroignore を使用する理由

- **セキュリティ** - Kiroが認証情報、APIキー、その他の機密データを含むファイルにアクセスするのを防止する
- **プライバシー** — AIとのやり取りから機密情報を除外する
- **コンプライアンス** — Kiroが外部サービスと共有すべきでないファイルにアクセスしないようにします
- **集中性** — 大容量のファイルやビルドアーティファクトを除外することで、Kiroのコンテキストを適切に維持する

## ワークスペースの無視設定

特定のプロジェクト内のファイルを除外するには、プロジェクトのルートディレクトリ（または任意のサブディレクトリ）に ``.kiroignore`` ファイルを作成し、除外したいファイルのパターンを追加します：

bash

```bash
# Secrets and credentials
.env
.env.*
!.env.example
*.pem
*.key

# Private directories
secrets/
private/
```

Kiroは、指定したパターンに一致するファイルをすべてスキップします。

IDE は、「**Agent Ignore Files**」設定から無視するファイル名を読み取ります。「設定」（Mac では `Cmd+,`、Windows/Linux では `Ctrl+,`）を開き、**「Agent Ignore Files**」（設定：`kiroAgent.agentIgnoreFiles`）を検索して、配列に `.kiroignore` を追加します。

`kiroAgent.agentIgnoreFiles`設定では、ファイル名の配列を指定できます：

- 複数の無視ファイルタイプを同時に使用する場合：`[".gitignore", ".kiroignore"]`
- ワークスペースレベルの無視ファイルを無効にするには、「`[]`」に設定します

### サブディレクトリの無視ファイル

`.gitignore`と同様に、サブディレクトリに`.kiroignore`ファイルを配置することで、親ディレクトリのパターンを上書きまたは拡張できます。サブディレクトリの無視ファイルに含まれるパターンは、そのサブディレクトリ内のファイルに対して優先されます。

## グローバル無視ファイル

Kiroは、グローバル無視ファイルが存在する場合、設定を必要とせずに自動的にそれを適用します：

- `~/.kiro/settings/kiroignore` - Kiroのグローバル無視パターン
- Gitのグローバル無視ファイル（git configの`core.excludesfile`で設定） - Gitリポジトリでのみ適用されます

すべてのプロジェクトに適用したいパターンには、グローバル無視ファイルを使用してください。

## パターンの構文

`.kiroignore` 標準の gitignore 構文を使用します:

| パターン | 効果 |
| --- | --- |
| `file.txt` | 特定のファイルを無視 |
| `*.log` | 拡張子による無視 |
| `folder/` | ディレクトリを無視する |
| `**/temp` | 任意のサブディレクトリを無視 |
| `!keep.txt` | 無視しない（否定） |

## 例

### API キーとシークレットの保護

bash

```bash
# Environment files with credentials
.env
.env.local
.env.production

# Keep the template accessible
!.env.example

# Certificate and key files
*.pem
*.key
*.p12
credentials/
```

### ビルド成果物やデータファイルを除外する

bash

```bash
# Build outputs
dist/
build/
.next/

# Data files
*.sql
*.dump
data/exports/
```

### チームのコンプライアンス設定

bash

```bash
# Customer data directories
customer-data/
pii/

# Audit and compliance docs
compliance/internal/
audit-reports/
```

## ベストプラクティス

- ファイルが無視される理由をコメントで記録する - チームメンバーにとって役立つ
- Kiroに無視されたファイルを読み込ませることでパターンをテストする――これにより、アクセスがブロックされていることが示される
- プロジェクトの構造が変化するにつれて、定期的にパターンを見直してください
- ワークスペース間で一貫した動作を確保するため、グローバルなパターンについてチームと調整する


---

[← 前へ: Compaction](compaction.md) | [↑ 親ページ](../../../index.md) | [次へ →: Checkpoints and rewind](checkpoints.md)
