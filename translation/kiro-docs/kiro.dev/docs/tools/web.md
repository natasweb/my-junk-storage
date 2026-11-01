# Web tools

> 元URL: https://kiro.dev/docs/tools/web/  
> 最終取り込み日: 2026-11-01  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

Webツールにより、Kiroはインターネット上の最新情報にリアルタイムでアクセスし、最新の検索結果やページコンテンツに基づいて応答を行うことができます。これらのツールは、意味のあるテキストの断片を複製しないように設計されており、一般に公開されているWebページに対応しています。

Webツールは、すべてのプラットフォーム（IDE、CLI、Web、モバイル）で利用可能です。

| ツール | 説明 |
| --- | --- |
| `web_search` | Web上で最新情報を検索する |
| `web_fetch` | URLからテキストを取得・抽出する |

以下のデモでは、エージェントが自動的に`web_search`を使用してクエリに対する最新の結果を返し、その後`web_fetch`を使用して特定のURLの最新コンテンツを取得します：

[](/videos/web-fetch-ide.mp4?h=72b1d375)

## web_search

ウェブを検索し、関連する結果を返します。エージェントは、時事問題、最近の変更、またはモデルの学習データに含まれていないトピックについて質問があった場合、これを自動的に使用します。

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
| `selective`（デフォルト） | 検索語句に一致する文字列の前後最大8,000文字を返す | 出力範囲を制限したターゲット抽出 |
| `truncated` | 最初の 8,000 文字 | クイックプレビュー |
| `full` | コンテンツ全体（最大 10 MB） | 包括的な分析 |

```text
> Get installation info from https://kiro.dev/blog/introducing-kiro-cli/

Fetching content from: https://kiro.dev/blog/introducing-kiro-cli/
(searching for: installation install getting started) [mode: selective]
✓ Fetched 7909 bytes (selective) from URL
```

特定のコンテンツが要求されない場合、ツールはコンテキストを管理するために、デフォルトで`truncated`モード（最初の8 KB）になります。

### URLの処理

IDE では、`web_fetch` は URL のクエリ文字列とフラグメントを保持し、取得前に HTTP URL を HTTPS にアップグレードします。URL にユーザー名やパスワードが含まれている場合、Kiro はその認証情報を削除し、結果に変更があったことを明記します。

### 制限事項

| 制約 | 値 |
| --- | --- |
| ページの最大サイズ | 10 MB |
| 最大選択出力数 | 8,000 文字 |
| タイムアウト | 30秒 |
| 最大リダイレクト回数 | 10 |
| CLI V3 リダイレクトポリシー | 別のオリジンへのリダイレクト、またはHTTPへのリダイレクトを拒否する |
| コンテンツタイプ | text/HTML のみ |
| CLI V3 一時的な障害時の再試行 | 1回の自動再試行 |

## ガバナンス（エンタープライズ）

IAM Identity Center を使用している管理者は、所属組織の Web ツールを無効にできます:

1. Kiroコンソールを開く
2. **[設定]** を選択します
3. [**共有設定]** で、[**Web 検索] および [Web フェッチ] ツールの**切り替えを [**オフ]** に設定します

詳細については、「[エンタープライズガバナンス - Webツール」](https://kiro.dev/docs/enterprise/governance/web-tools/)を参照してください。

## エージェントごとの Web ツールの無効化

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
