# リサーチ・設計根拠ログ

---
**目的**: ディスカバリーフェーズで得た調査結果・アーキテクチャ検討・意思決定の根拠を記録する。

---

## サマリー

- **フィーチャー**: `engineer-portfolio`
- **ディスカバリースコープ**: 新規フィーチャー（フルディスカバリー実施）
- **主な調査結果**:
  - Notion API公式SDK（`@notionhq/client` v5.15.0）はNext.js App RouterのISRと組み合わせて利用可能。APIレートリミットは平均3 req/秒（2700 req/15分）。
  - Zenn RSSフィードは `https://zenn.dev/{username}/feed` で提供。`rss-parser` ライブラリが最も適合性が高い。
  - コンタクトフォームのメール送信は **Resend** が小規模ポートフォリオサイトに最適（Next.js Server Actions との親和性が高い）。
  - キャッシュ戦略はNext.jsの `fetch` with `next: { revalidate }` でISRパターンを採用し、ビルド後も定期的にデータを更新する。

---

## リサーチログ

### Notion API 調査

- **コンテキスト**: 要件6でNotionをCMSとして利用し事例データを管理するため、公式SDKの仕様・制約を調査。
- **参照ソース**:
  - [@notionhq/client npm](https://www.npmjs.com/package/@notionhq/client)
  - [Notion API Reference - Post database query](https://developers.notion.com/reference/post-database-query)
  - [Notion API Reference - Request limits](https://developers.notion.com/reference/request-limits)
- **調査結果**:
  - 公式SDK: `@notionhq/client` v5.15.0（Node.js 18以上が必要）
  - データベースのクエリはステータスプロパティでフィルタリング可能（`filter.property.select.equals`）
  - レートリミット: 平均3 req/秒（2700 req/15分）、超過時はHTTP 429 + `Retry-After`ヘッダー
  - プロパティの型: `title`/`rich_text` はrich text objectsの配列、`select` は `{ id, name, color }` オブジェクト、`files` はファイルオブジェクトの配列、`checkbox` はboolean、`url` は文字列
- **実装への影響**:
  - 公開済み事例のフィルタリングは `filter: { property: "Status", select: { equals: "公開" } }` で実現
  - 取得したrich textはテキスト部分を連結して文字列として扱う必要がある
  - ISRの `revalidate` 時間内はキャッシュを利用し、APIコール頻度を抑制

### Zenn RSS フィード調査

- **コンテキスト**: 要件4でZenn記事一覧をRSS経由で表示するため、フィード仕様とパースライブラリを調査。
- **参照ソース**:
  - [ZennをRSSフィードで購読する - Zenn公式](https://zenn.dev/zenn/articles/zenn-feed-rss)
  - [rss-parser - npm](https://www.npmjs.com/package/rss-parser)
- **調査結果**:
  - フィードURL: `https://zenn.dev/{username}/feed`（`?all=1` で全件取得、デフォルトは最新約20件）
  - フィードアイテムフィールド: `title`, `link`, `pubDate`, `guid`, `description`（サマリー）
  - `rss-parser` ライブラリがRSS専用で最も使いやすい（`parseURL()` / `parseString()` でPromiseを返す）
- **実装への影響**:
  - Next.js `fetch` + `next: { revalidate: 3600 }` でRSSを1時間キャッシュ（要件4.6準拠）
  - `rss-parser` の型定義を利用して型安全にアイテムを取得

### メール送信サービス調査

- **コンテキスト**: 要件5でコンタクトフォームのメール送信機能を実装するため、サーバーレス環境に適したサービスを調査。
- **参照ソース**:
  - [Send emails with Next.js - Resend](https://resend.com/docs/send-with-nextjs)
  - [5 Best Email Services for Next.js - DEV Community](https://dev.to/ethanleetech/5-best-email-services-for-nextjs-1fa2)
- **調査結果**:
  - **Resend**: Next.js Server Actions対応、モダンなDX、小規模サイト向き（無料枠あり）
  - **SendGrid**: エンタープライズ向け、設定が複雑
  - **Nodemailer**: SMTP直接接続、ベンダーロックインなし、サーバーレス環境では別途SMTPホスト必要
- **実装への影響**:
  - Resendを採用（Next.js Route Handler + `resend` SDKで実装）
  - Server Actionsを利用してクライアントとのインターフェースをシンプルに保つ

### キャッシュ戦略調査

- **コンテキスト**: 要件6.2でNotionデータ・要件4.6でRSSデータのキャッシュ最適化を実現するため、Next.js App RouterのISRパターンを調査。
- **参照ソース**:
  - [Incremental Static Regeneration - Next.js Docs](https://nextjs.org/docs/app/guides/incremental-static-regeneration)
- **調査結果**:
  - `fetch()` に `next: { revalidate: seconds }` オプションを付与するだけでISRが機能
  - stale-while-revalidateパターン: キャッシュを即時配信しつつ、バックグラウンドで再生成
  - ページ単位の `export const revalidate = N` 設定も有効
- **実装への影響**:
  - 事例データ（Notion）: `revalidate: 300`（5分）で設定
  - ブログデータ（Zenn RSS）: `revalidate: 3600`（1時間）で設定
  - レートリミット到達時はキャッシュデータを使って継続表示（要件6.3準拠）

---

## アーキテクチャパターン評価

| オプション | 説明 | 強み | リスク/制限 | 備考 |
|-----------|------|------|------------|------|
| RSCファースト（採用） | Server Componentsを優先、インタラクティブ部分のみClient Component | バンドルサイズ最小化、パフォーマンス最適化、SEO有利 | フィルタ等インタラクティブ箇所はClient Component分離が必要 | ステアリングのRSCファースト設計原則に準拠 |
| ページルーター（Pages Router） | 全ページをgetStaticProps/getServerSidePropsで取得 | 実績豊富、移行コスト不要 | App Routerの恩恵なし、RSC使用不可 | 採用しない（ステアリングでApp Router採用済み） |
| フルCSR | Client Componentでデータフェッチ | 実装シンプル | SEO不利、パフォーマンス低下 | 採用しない |

---

## 設計決定

### 決定: Notion APIのデータフェッチ戦略

- **コンテキスト**: 事例データをNotionから取得してISR対応の静的ページを生成する必要がある
- **検討した選択肢**:
  1. ビルド時フル静的生成（`generateStaticParams` + `revalidate: 0`）— ビルド時のみ更新
  2. ISR（`generateStaticParams` + `revalidate: 300`）— 定期的にバックグラウンド再生成
  3. SSR（`cache: 'no-store'`）— 毎リクエスト都度取得
- **採用アプローチ**: ISR（オプション2）
- **根拠**: コンテンツの更新頻度（数時間〜数日）とパフォーマンス要件のバランスが良い。Notionレートリミット（3 req/秒）に対してもISRキャッシュが有効な緩衝材となる。
- **トレードオフ**: Notion更新からサイト反映まで最大5分のラグが生じるが、ポートフォリオサイトとして許容範囲内。
- **フォローアップ**: Notion Webhookによる即時再ビルドはオプションとして将来検討可。

### 決定: コンタクトフォームの実装方式

- **コンテキスト**: フォームバリデーション・ローディング状態・エラーハンドリングを含むコンタクトフォームを実装する
- **検討した選択肢**:
  1. Next.js Server Actions + `useActionState` — フォームとアクションの統合
  2. Route Handler（`/api/contact`）+ クライアントサイドfetch
  3. サードパーティフォームサービス（Formspree等）
- **採用アプローチ**: Route Handler（オプション2）
- **根拠**: Server Actionsはローディング状態や楽観的更新の実装がより複雑。Route Handlerはバリデーション・エラーハンドリング・レスポンス制御が明示的で設計意図が明確になる。
- **トレードオフ**: クライアントサイドfetchが必要だが、ContactFormはいずれにせよClient Componentのため追加コストは小さい。
- **フォローアップ**: レート制限（スパム防止）のためIP制限またはreCAPTCHA導入を検討。

### 決定: 事例フィルタリングの実装方式

- **コンテキスト**: 要件2.4/2.5で業種・技術スタックによるフィルタリングが必要
- **検討した選択肢**:
  1. クライアントサイドフィルタリング（全事例を一度取得してReact stateでフィルタ）
  2. URLクエリパラメータによるフィルタリング（`/cases?industry=SaaS`）
  3. サーバーサイドフィルタリング（フィルタ条件をNotion API queryに渡す）
- **採用アプローチ**: クライアントサイドフィルタリング（オプション1）
- **根拠**: 事例数が少量（〜50件程度）のため全件取得後のクライアントフィルタで十分。Notion APIへのリクエスト回数を最小化できる。URLベースにすることでSEO・共有リンクの利点があるが、要件では明示されていないため過剰実装を避ける。
- **トレードオフ**: 事例数が大幅に増加した場合は再設計が必要だが、ポートフォリオの性質上その可能性は低い。

---

## リスクと対策

- **Notion APIレートリミット超過** — ISRキャッシュで緩和。HTTP 429時はキャッシュデータで継続表示（要件6.3準拠）。
- **Notion APIスキーマ変更** — 型安全なマッピング層（`NotionRepository`）を設け、スキーマ変更の影響をlib層に局所化。
- **Zenn RSSフィード取得失敗** — フォールバックメッセージ + Zennプロフィールへの直接リンクを表示（要件4.3準拠）。
- **コンタクトフォームスパム** — 初期実装では最低限のサーバーサイドバリデーションのみ。必要に応じてRate LimitingやreCAPTCHAを追加。
- **Next.js 16 / React 19 の新機能による非互換** — `@notionhq/client` v5はNode.js 18+必須。現行ステアリングのNext.js 16は対応範囲内。

---

## 参考資料

- [@notionhq/client npm](https://www.npmjs.com/package/@notionhq/client) — 公式Notion JavaScript SDK
- [Notion API Reference](https://developers.notion.com/reference/intro) — APIリファレンス全般
- [Next.js ISR Docs](https://nextjs.org/docs/app/guides/incremental-static-regeneration) — ISRの実装パターン
- [rss-parser npm](https://www.npmjs.com/package/rss-parser) — RSS/Atomフィードパーサー
- [Resend - Send with Next.js](https://resend.com/docs/send-with-nextjs) — メール送信サービス統合ガイド
- [ZennのRSSフィード仕様](https://zenn.dev/zenn/articles/zenn-feed-rss) — Zenn公式RSS仕様説明
