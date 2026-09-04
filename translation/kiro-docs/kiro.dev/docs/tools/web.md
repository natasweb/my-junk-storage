# Web tools

> 元URL: https://kiro.dev/docs/tools/web/  
> 最終取り込み日: 2026-09-05  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

Webツールにより、Kiroはインターネット上の最新情報にリアルタイムでアクセスし、最新の検索結果やページコンテンツに基づいて応答を行うことができます。これらのツールは、意味のあるテキストの断片を複製しないように設計されており、一般に公開されているWebページに対応しています。

Webツールは、すべてのプラットフォーム（IDE、CLI、Web、モバイル）で利用可能です。

| ツール | 説明 |
| --- | --- |
| `web_search` | Web上で最新情報を検索する |
| `web_fetch` | URLからテキストを取得・抽出する |

以下のデモでは、エージェントが自動的に `web_search` を使用してクエリに対する最新の結果を取得し、その後 `web_fetch` を使用して特定の URL の最新コンテンツを取得します：

[](/videos/web-fetch-ide.mp4?h=72b1d375)

## web_search

ウェブを検索し、関連する結果を返します。現在の出来事、最近の変更、またはモデルの学習データに含まれていないトピックについて質問すると、エージェントはこれを自動的に使用します。

```text
> What is the latest on EC2 instances?

Searching the web for: AWS EC2 instances latest 2025 (using tool: web_search)
✓ Found 10 search results
```

## web_fetch

特定のURLからテキストコンテンツを取得・抽出します。コンテキストを効率的に管理するための3つのモードに対応しています。

### 取得モード

| モード | 動作 | ユースケース |
| --- | --- | --- |
| `selective` (デフォルト) | 検索語句に一致する前後文を返す | 対象を絞った抽出 |
| `truncated` | 最初の8,000文字 | クイックプレビュー |
| `full` | コンテンツ全体（最大10 MB） | 包括的な分析 |

```text
> Get installation info from https://kiro.dev/blog/introducing-kiro-cli/

Fetching content from: https://kiro.dev/blog/introducing-kiro-cli/
(searching for: installation install getting started) [mode: selective]
✓ Fetched 7909 bytes (selective) from URL
```

特定のコンテンツが要求されない場合、このツールはコンテキストを管理するために、デフォルトで「`truncated`」モード（最初の 8 KB）になります。

### 制限事項

| 制約 | 値 |
| --- | --- |
| 最大ページサイズ | 10 MB |
| タイムアウト | 30 秒 |
| 最大リダイレクト回数 | 10 |
| コンテンツタイプ | text/HTML のみ |
| 再試行回数 | 3回の自動再試行 |

## ガバナンス（エンタープライズ）

IAM Identity Center を使用する管理者は、所属組織の Web ツールを無効にできます:

1. Kiroコンソールを開く
2. **[設定]** を選択します
3. [**共有設定]** で、[**Web 検索] および [Web フェッチ] ツールのスイッチを [****オフ]** に切り替えます

詳細については、「[エンタープライズガバナンス - Webツール」](https://kiro.dev/docs/enterprise/governance/web-tools/)を参照してください。

## エージェントごとのWebツールの無効化

エージェントが Web ツールを使用できないようにするには、`web`タグを除外します:

json

```json
{
  "tools": ["read", "write", "shell"],
  "excludedTools": ["web"]
}
```

または、権限設定を使用して Web アクセスを拒否します:

yaml

```yaml
rules:
  - capability: web_fetch
    effect: deny
  - capability: web_search
    effect: deny
```

## 次の手順

- [組み込みツールの概要](https://kiro.dev/docs/tools/) - ツールカタログ一覧
- [権限](https://kiro.dev/docs/permissions/) - 機能ベースのアクセス制御の設定


---

[← 前へ: Built-in tools](index.md) | [↑ 親ページ](index.md) | [次へ →: Code intelligence](code-intelligence.md)
