## 技術スタック

### コア

| 役割 | 技術 |
| --- | --- |
| フレームワーク | Next.js 14 App Router |
| 言語 | TypeScript |
| スタイリング | Tailwind CSS |
| UIコンポーネント | shadcn/ui |

### コンテンツ管理

| 役割 | 技術 |
| --- | --- |
| Works管理 | Markdown（MDX） |
| MD処理 | next-mdx-remote or gray-matter + remark |
| Zenn RSS | fetch（ISR revalidate: 3600） |

### インフラ

| 役割 | 技術 |
| --- | --- |
| ホスティング | Vercel |
| ドメイン | お好みで（Vercel Domains or 外部） |

### SEO / OGP

| 役割 | 技術 |
| --- | --- |
| メタデータ管理 | Next.js Metadata API |
| OGP画像 | 静的画像（フェーズ1） / next/og（フェーズ2） |