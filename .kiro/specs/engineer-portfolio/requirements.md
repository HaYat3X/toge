# Requirements Document

## Project Description (Input)

「プロジェクトを推進できるエンジニア」としての信頼を、事例の読み物で証明するサイト。
技術力の羅列ではなく、**課題解決のストーリー**を軸に据える。

詳細な背景・コンテンツ戦略・ページ構成・機能要件・コンテンツ管理方針は仕様初期化時の記述を参照。

---

## イントロダクション

このドキュメントは、エンジニアポートフォリオサイト（toge）の機能要件インデックスである。
本サイトは潜在クライアントへの信頼構築・案件獲得をメインゴールとし、NotionをCMS、ZennをブログプラットフォームとしてRSS経由で連携する。

技術スタック: Next.js 16 (App Router) + TypeScript + Tailwind CSS + shadcn/ui

---

## Requirements Index

| # | ファイル | 内容 |
|---|---|---|
| 1 | [01-top-page.md](requirements/01-top-page.md) | トップ / 自己紹介ページ |
| 2 | [02-case-list.md](requirements/02-case-list.md) | 事例・実績一覧ページ |
| 3 | [03-case-detail.md](requirements/03-case-detail.md) | 事例詳細ページ |
| 4 | [04-blog.md](requirements/04-blog.md) | ブログページ（Zenn RSS連携） |
| 5 | [05-contact.md](requirements/05-contact.md) | お問い合わせページ |
| 6 | [06-notion.md](requirements/06-notion.md) | Notion連携（コンテンツ管理） |
| 7 | [07-responsive.md](requirements/07-responsive.md) | レスポンシブ対応 |
| 8 | [08-seo.md](requirements/08-seo.md) | SEO・パフォーマンス |
