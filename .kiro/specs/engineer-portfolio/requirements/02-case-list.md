# 要件 2: 事例・実績一覧ページ

**Objective:** 潜在クライアントとして、エンジニアの過去プロジェクト一覧を俯瞰したい。そうすることで、自分のニーズに合う事例を見つけて詳細を確認できる。

## Acceptance Criteria

1. The Portfolio Site shall 事例一覧ページに、Notionデータベースから取得した全事例をカード形式（サムネイル・タイトル・一言概要）で表示する。
2. When Notionから事例データが取得できたとき, the Portfolio Site shall 各カードをクリックすることで対応する事例詳細ページへ遷移できる状態にする。
3. If Notionからのデータ取得に失敗したとき, the Portfolio Site shall エラーメッセージを表示し、ページのクラッシュを防ぐ。
4. The Portfolio Site shall 事例一覧ページに、業種・使用技術によるフィルタリング機能を提供する。
5. When フィルタ条件が選択されたとき, the Portfolio Site shall 条件に合致する事例カードのみを表示する。
6. The Portfolio Site shall 事例一覧ページのメタ情報（titleタグ、OGPタグ）を適切に設定する。
