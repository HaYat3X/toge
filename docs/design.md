# Design System — Hayate Takamine Portfolio

v0 で生成したモックをベースに確定したデザイン仕様。
新しいページやコンポーネントを追加するときは必ずここを参照すること。

---

## コンセプト

**Vercel / Linear ライクなモノクロミニマル**

- 色で主張しない。余白・タイポグラフィ・微細なボーダーだけで情報を整理する
- 「読む気になれる静けさ」を最優先。装飾は必要最小限
- ホバーやトランジションで動きを出し、静止状態はとにかくクリーン

---

## カラー

shadcn/ui のセマンティックトークンのみ使用。ハードコードの hex は原則禁止。

| トークン | 用途 |
|---|---|
| `background` | ページ背景（ライト: `oklch(0.975 0 0)` / ダーク: `oklch(0.145 0 0)`） |
| `foreground` | メインテキスト |
| `card` | カード背景（白） |
| `muted-foreground` | サブテキスト、ラベル、日付 |
| `border` | ボーダー（`oklch(0.922 0 0)`） |
| `foreground/5` | 超薄背景（ホバー、バッジ） |
| `foreground/10` | 薄ボーダー（カード枠） |

アクセントカラーは使わない。唯一の例外は `bg-green-500`（Available バッジのパルスドット）のみ。

---

## タイポグラフィ

**フォント**: Geist（sans） / Geist Mono（mono）

| 用途 | クラス |
|---|---|
| Hero 見出し | `text-5xl sm:text-6xl lg:text-7xl xl:text-8xl font-bold tracking-tight leading-[1.1]` |
| セクション見出し（WORKS など） | `text-sm font-medium text-muted-foreground uppercase tracking-widest` |
| カードタイトル | `text-lg lg:text-xl font-semibold` |
| 本文 | `text-sm text-muted-foreground leading-relaxed` |
| 補足テキスト（日付・カテゴリ） | `text-xs text-muted-foreground` |
| フッターコピーライト | `text-xs text-muted-foreground` |

font-weight は `font-medium`（500）・`font-semibold`（600）・`font-bold`（700）の3種のみ。

---

## スペーシング・レイアウト

- コンテンツ最大幅: `max-w-6xl mx-auto px-6 lg:px-8`
- セクション上下余白: `py-24 lg:py-32`
- Hero 上余白: `pt-32 pb-24 lg:pt-40 lg:pb-32`（ fixed header 分を考慮）

---

## ボーダー・角丸

- カードのボーダー: `border border-foreground/5`（極薄）
- ホバー時ボーダー: `hover:border-foreground/10`
- 角丸: `--radius: 0.625rem`（≒ `rounded-md`）をベースに shadcn が自動計算
- ボタン（pill型）: `rounded-full`
- カード: `rounded-lg`（shadcn Card のデフォルト）

---

## シャドウ

- 静止: `shadow-sm`
- ホバー: `hover:shadow-lg`（カードのみ）
- それ以外のコンポーネントにシャドウは付けない

---

## 背景装飾

Hero セクションのみ装飾的な blur 円を使う。他セクションでは原則禁止。

```tsx
{/* Hero の装飾要素 — 他セクションで使い回さないこと */}
<div className="absolute top-20 right-0 w-[600px] h-[600px] bg-gradient-to-br from-neutral-200/40 to-transparent rounded-full blur-3xl -z-10" />
<div className="absolute bottom-0 left-0 w-[400px] h-[400px] bg-gradient-to-tr from-neutral-300/30 to-transparent rounded-full blur-3xl -z-10" />
```

ZennArticles のセクション背景グラデーション（`via-foreground/[0.02]`）は許容。

---

## コンポーネント別仕様

### Header

- `fixed top-0` + `backdrop-blur-md` + `bg-background/70`
- ロゴ: `w-8 h-8 bg-foreground text-background rounded-lg` のアイコン + 名前テキスト
- Nav リンク: `rounded-full hover:bg-foreground/5`
- CTA ボタン（Contact）: `bg-foreground text-background rounded-full`

### Hero

- 見出しは2行構成。1行目 `block`、2行目 `block mt-2 relative`
- 2行目に下線装飾: `absolute -bottom-2 left-0 w-full h-1 bg-gradient-to-r from-foreground/20 via-foreground/60 to-foreground/20 rounded-full`
- Available バッジ: `bg-foreground/5 border border-foreground/10 rounded-full` + `bg-green-500 animate-pulse` ドット
- 統計数字: `grid grid-cols-3`、区切りは `w-px bg-gradient-to-b from-transparent via-foreground/10 to-transparent`

### Works カード

- 構造: 番号サイドバー（`bg-foreground/[0.02] border-r`） + コンテンツ本体
- ホバー: `hover:shadow-lg hover:border-foreground/10`
- タイトルのホバーアニメーション: `group-hover:translate-x-1 transition-transform duration-300`
- 右矢印ボタン: `group-hover:bg-foreground group-hover:border-foreground` で反転

### Zenn カード

- サムネイル: `aspect-[16/9]` + グリッドパターン背景 + 絵文字（`text-5xl`）
- 絵文字ホバー: `group-hover:scale-110 transition-transform duration-300`
- いいね数バッジ: `bg-foreground/5 rounded-full`

### Footer / About / Contact

- 2カラムグリッド: `grid grid-cols-1 lg:grid-cols-2 gap-16 lg:gap-24`
- ボタン2種: `bg-foreground text-background rounded-md`（primary）/ `border border-border rounded-md`（outline）

---

## アニメーション・インタラクション

| 対象 | 挙動 |
|---|---|
| カード全般 | `transition-all duration-300` でシャドウ・ボーダーが変化 |
| Works タイトル | ホバーで `translate-x-1` |
| Zenn 絵文字 | ホバーで `scale-110` |
| 矢印 / リンク | ホバーで `translate-x-0.5` |
| Header Nav ロゴ | `hover:opacity-70 transition-opacity` |

`duration-300` を基本とし、それ以外の値は使わない。

---

## やってはいけないこと

- `oklch` や `#hex` の直書き（グラデーションの blur 装飾を除く）
- アクセントカラーの追加（緑・青など）
- Hero 以外でのフルスクリーン blur 装飾
- `font-weight: 400`（regular）のみの見出し → 最低でも `font-medium`
- カードに `rounded-2xl` 以上の大きい角丸
- シャドウを静止状態から付ける（`shadow-sm` は許容、`shadow-md` 以上は禁止）