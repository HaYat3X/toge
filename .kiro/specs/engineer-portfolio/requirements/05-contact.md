# 要件 5: お問い合わせページ

**Objective:** 潜在クライアントとして、エンジニアに仕事の依頼を簡単に連絡したい。そうすることで、案件の相談をスムーズに開始できる。

## Acceptance Criteria

1. The Portfolio Site shall お問い合わせページに、氏名・メールアドレス・件名・メッセージ本文の入力フォームを提供する。
2. When ユーザーがフォームを送信しようとしたとき, the Portfolio Site shall 必須項目（氏名・メールアドレス・メッセージ本文）が入力されているかバリデーションを行う。
3. If 必須項目が未入力の状態で送信が試みられたとき, the Portfolio Site shall 該当フィールドにエラーメッセージを表示し送信を阻止する。
4. If メールアドレスの形式が不正だったとき, the Portfolio Site shall メールアドレスフィールドにフォーマットエラーを表示する。
5. When フォームが正常に送信されたとき, the Portfolio Site shall 送信完了メッセージを表示し、フォームをリセットする。
6. If フォーム送信処理中にエラーが発生したとき, the Portfolio Site shall ユーザーにエラーを通知し、再送信を促すメッセージを表示する。
7. While フォームが送信処理中のとき, the Portfolio Site shall 送信ボタンを無効化してローディング状態を表示する。
