# 実装計画

- [ ] 1. プロジェクト基盤セットアップ
- [ ] 1.1 依存パッケージのインストールと環境変数設定
  - `@notionhq/client`・`rss-parser`・`resend` を package.json に追加しインストールする
  - `.env.local` に `NOTION_API_KEY`・`NOTION_DATABASE_ID`・`RESEND_API_KEY`・`RESEND_FROM_EMAIL`・`CONTACT_TO_EMAIL` を定義する
  - `.env.local.example` に各変数のプレースホルダを記載する
  - _Requirements: 6.1, 5.5_

- [ ] 1.2 共通型定義の作成
  - `CaseItem`（title, slug, thumbnail, summary, problem, approach, result, techStack, relatedZennUrl, status）の型を定義する
  - `BlogPost`（title, pubDate, summary, link）の型を定義する
  - `ContactPayload`（name, email, subject, message）の型を定義する
  - _Requirements: 6.1, 4.1, 5.1_

- [ ] 2. データ層の実装

- [ ] 2.1 (P) NotionRepositoryの実装
  - Notion APIクライアントを初期化し、環境変数から認証情報を読み込む
  - `Status="公開"` フィルタを適用して事例一覧を取得する `getAllCases()` を実装する
  - スラッグを受け取り単一事例を取得する `getCaseBySlug(slug)` を実装する
  - `next: { revalidate: 300 }` を付与した fetch でISRキャッシュを制御する
  - Notionプロパティのマッピング時に不正フィールドを検出したらエラーログを出力してスキップする
  - Notion APIが429を返した場合、既存キャッシュを継続利用する（スローしない）
  - _Requirements: 2.1, 2.3, 3.1, 3.5, 6.1, 6.2, 6.3, 6.4, 6.5_

- [ ] 2.2 (P) ZennFeedServiceの実装
  - `rss-parser` でZenn RSSフィードを取得・パースして `BlogPost[]` を返す `fetchPosts()` を実装する
  - `next: { revalidate: 3600 }` を付与した fetch で1時間キャッシュを制御する
  - フィード取得失敗時は例外をキャッチしてnullを返し、呼び出し元でフォールバック判定できるようにする
  - 返却前に公開日降順でソートする
  - _Requirements: 4.1, 4.2, 4.3, 4.6_

- [ ] 2.3 (P) ContactServiceの実装
  - Resend SDKを初期化し、フォームデータをメールとして送信する `sendContactEmail(payload)` を実装する
  - 送信成功時は `{ success: true }` を、失敗時は `{ success: false, error: string }` を返す
  - _Requirements: 5.5, 5.6_

- [ ] 2.4 (P) MetadataBuilderの実装
  - ページ種別（トップ・一覧・詳細・ブログ・コンタクト）ごとに `title`・`description`・OGPタグを生成するヘルパー関数を実装する
  - 事例詳細ページ向けにはタイトルと概要を動的に受け取り生成する
  - _Requirements: 1.4, 2.6, 3.6, 8.1, 8.2_

- [ ] 3. グローバルレイアウト・ナビゲーションの実装

- [ ] 3.1 Headerとデスクトップナビゲーションの実装
  - `src/components/layout/Header.tsx` を作成し、サイトロゴ・デスクトップナビゲーションリンク（事例一覧・ブログ・お問い合わせ）を配置する
  - `src/app/layout.tsx` に Header を組み込み全ページに適用する
  - リンクは Next.js `<Link>` を使用してクライアントサイドナビゲーションを有効にする
  - _Requirements: 1.3, 7.1_

- [ ] 3.2 MobileNavigationの実装
  - ハンバーガーアイコンでトグルするドロワー型メニューを `src/components/layout/MobileNavigation.tsx` として実装する（Client Component）
  - 767px以下でハンバーガーを表示し、768px以上では非表示とするブレークポイントを設定する
  - メニュー項目のタップ操作に適切なタッチターゲットサイズ（最低44px）を確保する
  - _Requirements: 7.1, 7.2, 7.3_

- [ ] 4. (P) トップページの実装

- [ ] 4.1 (P) HeroSectionおよびSkillsSectionの実装
  - `src/components/top/HeroSection.tsx` を作成し、メインキャッチコピーとサブコピーをファーストビューに表示する
  - `src/components/top/SkillsSection.tsx` を作成し、得意領域・スキル概要を列挙表示する
  - モバイル・タブレット・デスクトップで最適化されたレイアウトを適用する
  - _Requirements: 1.1, 1.2, 7.1_

- [ ] 4.2 FeaturedCasesの実装
  - `src/components/top/FeaturedCases.tsx` を作成し、NotionRepositoryから代表事例を最大3件取得して `CaseCard` で表示する
  - 事例一覧ページへの「もっと見る」リンクを設ける
  - _Requirements: 1.5_

- [ ] 4.3 トップページの統合とメタデータ設定
  - `src/app/page.tsx` に HeroSection・SkillsSection・FeaturedCases を組み合わせてページを完成させる
  - MetadataBuilder を使用してトップページの `metadata` オブジェクトを設定する
  - _Requirements: 1.3, 1.4_

- [ ] 5. (P) 事例一覧ページの実装

- [ ] 5.1 (P) CaseCardとCaseGridの実装
  - `src/components/cases/CaseCard.tsx` を作成し、サムネイル（Next.js Image）・タイトル・一言概要・リンクを表示する
  - `src/components/cases/CaseGrid.tsx` を作成し、`CaseCard` のグリッド一覧とNotionデータ取得失敗時のエラー表示を担当する
  - _Requirements: 2.1, 2.2, 2.3, 7.4, 8.5_

- [ ] 5.2 CaseFilterBarの実装
  - `src/components/cases/CaseFilterBar.tsx` を Client Component として実装し、業種・使用技術のフィルタ選択UIを提供する
  - フィルタ条件の変更時に親から渡された一覧データをクライアントサイドでフィルタリングして表示する
  - _Requirements: 2.4, 2.5_

- [ ] 5.3 事例一覧ページの統合とメタデータ設定
  - `src/app/cases/page.tsx` に CaseGrid と CaseFilterBar を組み合わせてページを完成させる
  - MetadataBuilder を使用して事例一覧ページの `metadata` オブジェクトを設定する
  - _Requirements: 2.6_

- [ ] 6. (P) 事例詳細ページの実装

- [ ] 6.1 CaseDetailContentの実装
  - `src/components/cases/CaseDetailContent.tsx` を作成し、Before（課題・背景）→ アプローチ・意思決定 → After（成果・インパクト）の構造でコンテンツを表示する
  - `src/components/cases/TechStackBadge.tsx` を作成し、使用技術スタックをバッジ形式で表示する
  - `src/components/cases/RelatedZennLink.tsx` を作成し、関連Zenn記事URLが設定されている場合のみリンクを表示し、クリックで新タブを開く
  - _Requirements: 3.1, 3.2, 3.3, 3.4_

- [ ] 6.2 事例詳細ページのルーティング・404対応・メタデータ設定
  - `src/app/cases/[slug]/page.tsx` を作成し、`generateStaticParams` で全事例スラッグの静的ルートを生成する
  - スラッグが存在しない場合は `notFound()` を呼び出して404を表示する
  - MetadataBuilder を使用して事例内容に基づいた `generateMetadata` を実装する
  - _Requirements: 3.5, 3.6, 3.7_

- [ ] 7. (P) ブログページの実装

- [ ] 7.1 (P) BlogPostCardとBlogPostListの実装
  - `src/components/blog/BlogPostCard.tsx` を作成し、記事タイトル・公開日・サマリーを表示し、クリックで新タブにZenn記事を開く
  - `src/components/blog/BlogFallback.tsx` を作成し、RSS取得失敗時にメッセージとZennプロフィールリンクを表示する
  - `src/components/blog/BlogPostList.tsx` を作成し、`BlogPostCard` のリストと失敗時の `BlogFallback` を切り替えて表示する
  - _Requirements: 4.1, 4.2, 4.3, 4.4_

- [ ] 7.2 ブログページの統合
  - `src/app/blog/page.tsx` を作成し、ZennFeedService からの記事一覧を `BlogPostList` で表示する
  - ブログを独立したルートとして提供し、事例一覧ページとは別ルートになっていることを確認する
  - _Requirements: 4.5_

- [ ] 8. (P) お問い合わせページの実装

- [ ] 8.1 /api/contact Route Handlerの実装
  - `src/app/api/contact/route.ts` を作成し、POST リクエストのボディをサーバーサイドでバリデーションする
  - バリデーション通過後に ContactService.sendContactEmail を呼び出し、結果をJSONレスポンスで返す
  - _Requirements: 5.2, 5.5, 5.6_

- [ ] 8.2 ContactFormコンポーネントの実装
  - `src/components/contact/ContactForm.tsx` を Client Component として実装し、氏名・メールアドレス・件名・メッセージ本文のフォームを提供する
  - 必須項目の未入力チェックとメールアドレス形式チェックをクライアントサイドで行い、エラーメッセージを各フィールドに表示する
  - 送信中はボタンを無効化してローディング状態を表示し、成功時は完了メッセージを表示してフォームをリセット、失敗時は再送信を促すエラーメッセージを表示する
  - _Requirements: 5.1, 5.2, 5.3, 5.4, 5.5, 5.6, 5.7_

- [ ] 8.3 お問い合わせページの統合
  - `src/app/contact/page.tsx` を作成し、ContactForm を組み込んでページを完成させる
  - _Requirements: 5.1_

- [ ] 9. SEO・パフォーマンス最適化と統合確認

- [ ] 9.1 sitemap.tsの生成
  - `src/app/sitemap.ts` を作成し、全静的ページ（トップ・事例一覧・ブログ・コンタクト）と NotionRepository から取得した全事例詳細ページのURLを含む `sitemap.xml` を出力する
  - _Requirements: 8.3_

- [ ] 9.2 全ページのレスポンシブ・タッチ対応の最終確認
  - 全コンポーネントにモバイル（〜767px）・タブレット（768px〜1023px）・デスクトップ（1024px〜）のブレークポイント対応が適用されているか確認し、未対応箇所を修正する
  - インタラクティブ要素（ボタン・リンク・フィルタ）のタッチターゲットサイズと操作性を確認する
  - _Requirements: 7.1, 7.2, 7.3_

- [ ] 9.3 画像最適化とCore Web Vitalsの対応確認
  - 全ての `<img>` 要素を Next.js `<Image>` コンポーネントに置き換え、`sizes` 属性を適切に設定してレスポンシブ配信を実現する
  - LCP対象となるファーストビュー画像に `priority` 属性を付与してプリロードを促進する
  - CLS防止のため画像に明示的な `width`・`height` を設定する
  - _Requirements: 7.4, 8.4, 8.5_
