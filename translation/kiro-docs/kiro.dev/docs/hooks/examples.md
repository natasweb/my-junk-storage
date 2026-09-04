# Hook examples

> 元URL: https://kiro.dev/docs/hooks/examples/  
> 最終取り込み日: 2026-09-05  
> このページは自動翻訳（英語→日本語）です。コード部分は翻訳していません。

---

これらの例は、ご自身のプロジェクトに合わせて応用できる、実用的なフックの実装例を示しています。各例には、トリガータイプ、ターゲットパターン、およびフックの完全な実装手順が含まれています。

## セキュリティ用プレコミットスキャナー

このフックは、コミット前にファイルをスキャンすることで、セキュリティ上の情報漏洩を防ぐのに役立ちます。

**トリガータイプ：**Agent Stop

**エージェントのプロンプト：**

```text
Review changed files for potential security issues:
1. Look for API keys, tokens, or credentials in source code
2. Check for private keys or sensitive credentials
3. Scan for encryption keys or certificates
4. Identify authentication tokens or session IDs
5. Flag passwords or secrets in configuration files
6. Detect IP addresses containing sensitive data
7. Find hardcoded internal URLs
8. Spot database connection credentials

For each issue found:
1. Highlight the specific security risk
2. Suggest a secure alternative approach
3. Recommend security best practices
```

## ユーザープロンプトの一元的なログ記録

このフックは、分析および監査のために、すべてのユーザープロンプトを一元化されたロギングシステムに記録します。

**トリガータイプ:** プロンプト送信

**シェルコマンド:**

bash

```bash
# Log user prompt to Grafana Loki
curl -H "Content-Type: application/json" -XPOST \
     "http://loghost/loki/api/v1/push" --data-raw \
     "{'streams': [{
        'stream': { 'app': 'kiro', 'user': \"${USER}\"  },
        'values': [ [\"$(date +%s%N)\", \"${USER_PROMPT}\"] ]
      }]}"
```

`USER_PROMPT`環境変数には、送信されたプロンプトテキストが格納されます。

## 国際化ヘルパー

このフックは、メインの言語ファイル内のテキストを更新した際に、翻訳が常に同期されるようにします。

**トリガータイプ:**
ファイル保存  
**ターゲット:**
`src/locales/en/*.json`

**エージェントプロンプト:**

```text
When an English locale file is updated:
1. Identify which string keys were added or modified
2. Check all other language files for these keys
3. For missing keys, add them with a "NEEDS_TRANSLATION" marker
4. For modified keys, mark them as "NEEDS_REVIEW"
5. Generate a summary of changes needed across all languages
```

## テストカバレッジの維持

このフックは、コードの進化に伴いテストカバレッジが常に高い水準を維持されるようにします。

**トリガータイプ:**
ファイルの保存  
**対象:**
`src/**/*.{js,ts,jsx,tsx}`

**エージェントのプロンプト:**

```text
When a source file is modified:
1. Identify new or modified functions and methods
2. Check if corresponding tests exist and cover the changes
3. If coverage is missing, generate test cases for the new code
4. Run the tests to verify they pass
5. Update coverage reports
```

## MCP との統合

エージェントフックは、Model Context Protocol (MCP) の機能を活用することで、その機能を拡張できます:

1. **外部ツールへのアクセス**: フックはMCPサーバーを利用して、専用のツールやAPIにアクセスできます
2. **コンテキストの強化**：MCPは、より高度なフックアクションを実現するための追加コンテキストを提供します
3. **ドメイン固有の知識**：専用のMCPサーバーは、ドメインに関する専門知識を提供できます

フックでMCPを使用するには：

1. [MCPサーバーを設定する](https://kiro.dev/docs/mcp/configuration/)
2. フックの指示内でMCPツールを参照する
3. 頻繁に使用するツールに対して、適切な自動承認設定を行う

**ユースケース：**

- Figmaのデザインシステムが確実に反映されるようにする
- タスク完了後にチケットのステータスを更新する
- プロジェクトフォルダ内のサンプルファイルからデータベースを同期する

### 例：Figmaデザインの検証

このフックは、HTMLおよびCSSファイルを監視し、Figma MCPを使用して、それらがFigmaのデザインに従っているかどうかを検証します。

**トリガータイプ：**
ファイル保存  
**ターゲット：**`*.css`
`*.html`

**エージェントプロンプト：**

```text
Use the Figma MCP to analyze the updated html or css files and check that they follow
established design patterns in the figma design. Verify elements like hero sections,
feature highlights, navigation elements, colors, and button placements align.
```


---

[← 前へ: Hook actions](actions.md) | [↑ 親ページ](index.md) | [次へ →: Management](management.md)
