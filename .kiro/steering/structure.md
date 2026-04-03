# Project Structure

## Organization Philosophy

`src/` 配下をレイヤー別に分割するアーキテクチャ。  
機能が増えた場合は `src/features/` ディレクトリを設けるフィーチャースライス方式も検討する。

## Directory Patterns

### App Router ページ
**Location**: `src/app/`  
**Purpose**: ルーティング・レイアウト・ページコンポーネント  
**Example**: `src/app/layout.tsx`（ルートレイアウト）、`src/app/page.tsx`（トップページ）

### UI コンポーネント
**Location**: `src/components/ui/`  
**Purpose**: shadcn/ui パターンで管理する再利用可能な UI プリミティブ。ビジネスロジックは含まない。  
**Example**: `src/components/ui/button.tsx`

### ユーティリティ
**Location**: `src/lib/`  
**Purpose**: 汎用ヘルパー関数（フレームワーク非依存）  
**Example**: `src/lib/utils.ts`（`cn()` 関数）

## Naming Conventions

- **ファイル**: PascalCase（コンポーネント: `Button.tsx`）、kebab-case（ページ: `page.tsx`、ユーティリティ: `utils.ts`）
- **コンポーネント**: PascalCase の関数コンポーネント（`function Button(...)`）
- **関数/変数**: camelCase

## Import Organization

```typescript
// 外部ライブラリ
import * as React from "react"
import { cva } from "class-variance-authority"

// 内部（絶対パス）
import { cn } from "@/lib/utils"

// 内部（相対パス）— 同ディレクトリ内のみ
import { local } from "./local"
```

**Path Aliases**:
- `@/` → `./src/`（`tsconfig.json` の `paths` で定義）

## Code Organization Principles

- UI コンポーネントはコンポーネント本体と `variants` をひとつのファイルにまとめてエクスポートする
- `cn()` を必ず使って className を合成する（直接文字列結合禁止）
- Server Components を優先し、インタラクティブな部分にのみ `"use client"` を付与する

---
_Document patterns, not file trees. New files following patterns shouldn't require updates_
