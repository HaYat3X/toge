# 要件 3: 事例詳細ページ

**Objective:** 潜在クライアントとして、特定プロジェクトの「課題 → アプローチ → 成果」のストーリーを詳しく読みたい。そうすることで、エンジニアへの発注判断ができる。

## Acceptance Criteria

1. The Portfolio Site shall 事例詳細ページに、Before（課題・背景）→ アプローチ・意思決定 → After（成果・インパクト）の構造でコンテンツを表示する。
2. The Portfolio Site shall 事例詳細ページに、使用技術スタックを補足情報として表示する。
3. Where 関連Zenn記事が設定されている場合, the Portfolio Site shall 事例詳細ページに関連Zenn記事へのリンクを表示する。
4. When ユーザーが関連Zenn記事リンクをクリックしたとき, the Portfolio Site shall 新しいタブでZenn記事ページを開く。
5. The Portfolio Site shall 各事例詳細ページに固有のURLスラッグを持つ静的なルートを生成する。
6. The Portfolio Site shall 事例詳細ページのメタ情報（titleタグ、OGPタグ、メタディスクリプション）を事例内容に基づいて設定する。
7. If 存在しない事例スラッグへのアクセスがあったとき, the Portfolio Site shall 404ページを表示する。
