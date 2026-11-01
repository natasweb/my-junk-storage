# Tool Search

> 元URL: https://kiro.dev/docs/mcp/tool-search/  
> 最終取り込み日: 2026-11-01  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

「ツール検索」機能により、各リクエストから未使用のMCPツール定義が除外されます。これにより、Kiroが必要なツールを読み込んでいる間も、作業に利用できるモデルコンテキストをより多く確保できます。

| 機能 | IDE | CLI | Web | モバイル |
| --- | --- | --- | --- | --- |
| ツールの検索 | ✓ | ✓ | — | — |

## 利用状況

Kiro IDE 1.3 以降では、ツール検索が自動的に使用されます。Kiro CLI 2.31 以降のローカル V3 セッションでは、デフォルトでツール検索が使用されます。[MCP サーバー](https://kiro.dev/docs/mcp/configuration/)が設定されていない場合、ツール検索は機能しません。

## V2 セッションでツール検索を有効にする

V2 セッションでは、ツール検索は引き続きオプトイン方式です。次のコマンドで有効にします:

bash

```bash
kiro-cli settings toolSearch.enabled true
```

V2では、MCPツールの定義が設定された閾値（デフォルトではコンテキストウィンドウの5%または50,000トークン）のいずれかに達すると、ツール検索が有効になります。MCPツールが存在するたびに有効にするには、両方の閾値を`0`に設定します:

bash

```bash
kiro-cli settings toolSearch.minPct 0
kiro-cli settings toolSearch.minTokens 0
```

### V2の設定

| 設定 | デフォルト | 説明 |
| --- | --- | --- |
| `toolSearch.enabled` | `false` | ツール検索を有効にする |
| `toolSearch.minPct` | `5` | MCP ツール定義がこのコンテキストウィンドウの割合を超えた場合に有効にする |
| `toolSearch.minTokens` | `50000` | MCP ツール定義がこのトークン数を超えたときに有効にする |

両方のしきい値が設定されている場合、いずれか一方を満たすとツール検索が有効になります。両方が`0`の場合、設定済みのMCPツールがツール検索を有効にします。

## V3 の設定

V3はデフォルトで、オンデマンドでMCPツールをロードします。Kiro CLI 2.31.1以降では、保存された`toolSearch.enabled`、`toolSearch.minPct`、および`toolSearch.minTokens`の値が、対話型および非対話型の両方のV3実行に適用されます。保存されたしきい値は、`toolSearch.enabled`も保存されている場合にのみ適用されます。

## オンデマンド読み込みの確認

「ツール検索」が有効な場合：

- IDEおよびローカルのV3アクティビティでは、MCPツールについて`tool_load`が`tool_call`よりも先に表示される場合があります
- V2では、`/tools`は、すべての定義をプリロードする場合よりもMCPツールトークンの使用量が少ない値を示します

## 仕組み

1. **インデックス作成**：Kiroは、接続されたMCPサーバーからツールの名前と説明をインデックス化します。
2. **遅延カタログ**：モデルは、すべての完全なツール定義の代わりに、要約されたリストを受け取ります。
3. **定義の読み込み**：Kiro は、必要なツールの完全な説明と入力スキーマを読み込みます。
4. **ツールの呼び出し**: Kiro は、通常の権限およびフックチェックを経て、読み込まれたツールを呼び出します。

IDE およびローカルの V3 セッションでは、`tool_load` が正確なツール ID を読み込み、`tool_call` がそれらを実行します。V2 では、有効にするとレガシーな `tool_search` パスが使用されます。

## 次のステップ

- [組み込みツール](https://kiro.dev/docs/reference/built-in-tools/)：Kiro に付属するツール
- [設定リファレンス](https://kiro.dev/docs/reference/settings/)：ツール検索の設定
- [MCP の設定](https://kiro.dev/docs/mcp/configuration/)：MCP サーバーの設定
- [ターミナル UI](https://kiro.dev/docs/cli/terminal-ui/)：ツールのアクティビティを表示する


---

[← 前へ: Tools](usage.md) | [↑ 親ページ](index.md) | [次へ →: Examples](examples.md)
