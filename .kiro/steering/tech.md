# Technology Stack

## Architecture

Next.js App Router を採用したサーバー/クライアント混在アーキテクチャ。  
ページ・レイアウトは `src/app/` に配置し、RSC を優先してクライアントコンポーネントは必要な範囲に限定する。

## Core Technologies

- **Language**: TypeScript 5（strict mode）
- **Framework**: Next.js 16 (App Router)
- **Runtime**: Node.js
- **UI Library**: React 19
- **Styling**: Tailwind CSS v4

## Key Libraries

| ライブラリ | 用途 |
|---|---|
| `shadcn` + `radix-ui` | アクセシブルな UI プリミティブ |
| `class-variance-authority` | コンポーネントバリアント定義 |
| `clsx` + `tailwind-merge` | className の条件合成（`cn()` ユーティリティ） |
| `lucide-react` | アイコン |

## Development Standards

### Type Safety
- TypeScript strict mode 必須（`tsconfig.json` で `"strict": true`）
- `any` 型は原則禁止

### Code Quality
- ESLint（`eslint-config-next`）

### Styling
- Tailwind CSS クラスは `cn()` ユーティリティでマージする（`src/lib/utils.ts`）
- CSS 変数でテーマカラーを管理（`--font-geist-sans`, `--font-geist-mono` など）

## Development Environment

### Common Commands

```bash
# Dev: npm run dev
# Build: npm run build
# Lint: npm run lint
```

## Key Technical Decisions

- **App Router**: Pages Router ではなく App Router を採用。RSC ファーストの設計。
- **shadcn/ui パターン**: コンポーネントをコピーして `src/components/ui/` に配置し、プロジェクト内でカスタマイズ可能にする。
- **Tailwind v4**: PostCSS プラグイン経由で使用（`@tailwindcss/postcss`）。

---
_Document standards and patterns, not every dependency_
