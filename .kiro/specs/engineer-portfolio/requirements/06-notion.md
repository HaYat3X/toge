# 要件 6: Notion連携（コンテンツ管理）

**Objective:** コンテンツ管理者（エンジニア自身）として、Notionで事例データを管理し、サイトに反映させたい。そうすることで、コードを書かずにコンテンツを更新できる。

## Acceptance Criteria

1. The Portfolio Site shall Notion APIを介して、指定されたデータベースから事例データ（タイトル・サムネイル・概要・課題・アプローチ・成果・技術スタック・関連Zennリンク等）を取得する。
2. The Portfolio Site shall Notion APIの取得結果をビルド時またはサーバーサイドでキャッシュし、表示パフォーマンスを最適化する。
3. If Notion APIのレートリミットに達したとき, the Portfolio Site shall キャッシュされたデータを使って表示を継続する。
4. The Portfolio Site shall Notion上で「公開」ステータスに設定された事例のみをサイトに表示する。
5. When Notionデータベースのスキーマが不正または必須フィールドが欠損しているとき, the Portfolio Site shall エラーログを出力しつつ、対象外のエントリをスキップして他の事例を正常表示する。
