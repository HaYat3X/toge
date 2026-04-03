# 要件 4: ブログページ（Zenn RSS連携）

**Objective:** 技術者・採用担当として、エンジニアの技術的な深掘り記事一覧を確認したい。そうすることで、技術レベルや思考スタイルを評価できる。

## Acceptance Criteria

1. The Portfolio Site shall ブログページに、ZennのRSSフィードを取得して記事一覧（タイトル・公開日・サマリー）を表示する。
2. When ZennのRSSフィードが正常に取得できたとき, the Portfolio Site shall 記事を公開日の降順で表示する。
3. If ZennのRSSフィード取得に失敗したとき, the Portfolio Site shall フォールバックメッセージを表示し、直接Zennプロフィールへのリンクを提供する。
4. When ユーザーが記事タイトルをクリックしたとき, the Portfolio Site shall 新しいタブでZennの該当記事ページを開く。
5. The Portfolio Site shall ブログページを事例一覧ページとは独立したルートとして提供する。
6. The Portfolio Site shall ブログページのRSSフィード取得結果を適切な期間（例: 1時間）キャッシュして、外部APIへのリクエスト頻度を最適化する。
