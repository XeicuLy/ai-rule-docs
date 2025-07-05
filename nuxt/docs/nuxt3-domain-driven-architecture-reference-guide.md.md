# Nuxt3 ドメイン駆動アーキテクチャ 参考ガイド

## 目次

- [設計概要](#設計概要)
- [ディレクトリ構造](#ディレクトリ構造)
- [基本ルール](#基本ルール)
- [命名規則](#命名規則)
- [依存関係ルール](#依存関係ルール)
- [開発フロー](#開発フロー)
- [プロジェクトへの適用](#プロジェクトへの適用)

## 設計概要

### コンセプト

この設計では**ページドメイン（機能ドメイン）**を基軸とした組織化を行います。Nuxt3 の`pages/`ディレクトリと`components/`ディレクトリを 1 対 1 で対応させることで、直感的で保守しやすいコード構造を実現します。

> **注意**: 以下の例（機能 A、機能 B、機能 C 等）は参考例です。実際のプロジェクトでは、あなたのビジネスドメインに応じて適切な機能名に置き換えてください。

### 核となる原則

1. **ページドメイン対応**: `pages/[功能]/` ↔ `components/[機能]/`
2. **機能別組織化**: 技術レイヤーではなくビジネス機能で分類
3. **明示的インポート**: オートインポート無効で AI 開発支援
4. **Nuxt3 完全準拠**: 公式ディレクトリ構造の 100%活用

### 対応関係の例

```
pages/feature-a/         ↔ components/feature-a/         （例：認証系機能）
pages/feature-b/         ↔ components/feature-b/         （例：エンティティ管理）
pages/feature-c/         ↔ components/feature-c/         （例：状態管理系機能）
pages/feature-d/         ↔ components/feature-d/         （例：ユーザー管理）
pages/feature-e/         ↔ components/feature-e/         （例：管理機能）
```

### コンポーネント階層（命名で区別）

```
components/
├── ui/                  # 基本UIコンポーネント（Button, Input等）
├── layout/              # レイアウトコンポーネント（Header, Footer等）
├── feature-a/           # 機能A（Index.vue + 子コンポーネント群）
├── feature-b/           # 機能B（Index.vue + 子コンポーネント群）
├── feature-c/           # 機能C（Index.vue + 子コンポーネント群）
└── feature-d/           # 機能D（Index.vue + 子コンポーネント群）
```

### ドメイン内構造（Index.vue を起点）

```
components/feature-a/          # 例：認証系機能
├── Index.vue               # 🎯 機能Aページのメインテンプレート
├── FormA.vue              # 例：ログインフォーム
├── FormB.vue              # 例：登録フォーム
├── FormC.vue              # 例：パスワードリセット
├── ProviderComponent.vue   # 例：認証プロバイダー
└── ValidationComponent.vue # 例：バリデーション表示

components/feature-b/          # 例：エンティティ管理
├── Index.vue              # 🎯 機能Bページのメインテンプレート
├── ItemCard.vue           # 例：アイテムカード
├── ItemList.vue           # 例：アイテムリスト
├── ItemDetail.vue         # 例：アイテム詳細
├── SearchComponent.vue    # 例：検索機能
└── FilterComponent.vue    # 例：フィルター機能
```

## ディレクトリ構造

### 全体構造

```
project-root/
└── src/
  ├── app.vue                     # ルートコンポーネント
  ├── types/                      # TypeScript型定義
  │   ├── index.ts                # 共通型
  │   ├── feature-a.ts            # 機能A型
  │   ├── feature-b.ts            # 機能B型
  │   ├── feature-c.ts            # 機能C型
  │   └── api.ts                  # API型
  ├── components/                 # コンポーネント（機能ドメイン別）
  │   ├── ui/                     # 基本UIコンポーネント
  │   ├── layout/                 # レイアウトコンポーネント
  │   ├── feature-a/              # 機能Aコンポーネント
  │   ├── feature-b/              # 機能Bコンポーネント
  │   ├── feature-c/              # 機能Cコンポーネント
  │   ├── feature-d/              # 機能Dコンポーネント
  │   ├── feature-e/              # 機能Eコンポーネント
  │   └── home/                   # ホーム機能コンポーネント
  ├── composables/                # Vue Composables（機能ドメイン別）
  │   ├── feature-a/              # 機能Aロジック
  │   ├── feature-b/              # 機能Bロジック
  │   ├── feature-c/              # 機能Cロジック
  │   ├── feature-d/              # 機能Dロジック
  │   └── common/                 # 共通ロジック
  ├── utils/                      # ユーティリティ関数
  │   ├── format.ts               # フォーマット関数
  │   ├── validation.ts           # バリデーション関数
  │   ├── constants.ts            # 定数定義
  │   └── api.ts                  # API設定
  ├── stores/                     # Pinia状態管理（機能ドメイン別）
  │   ├── feature-a.ts            # 機能A状態
  │   ├── feature-b.ts            # 機能B状態
  │   ├── feature-c.ts            # 機能C状態
  │   ├── feature-d.ts            # 機能D状態
  │   └── app.ts                  # アプリ全体状態
  ├── pages/                      # Nuxt3ページ（コンポーネントと対応）
  │   ├── index.vue               # トップページ
  │   ├── feature-a/              # 機能Aページ群
  │   ├── feature-b/              # 機能Bページ群
  │   ├── feature-c/              # 機能Cページ群
  │   ├── feature-d/              # 機能Dページ群
  │   └── feature-e/              # 機能Eページ群
  ├── layouts/                    # Nuxt3レイアウト
  │   ├── default.vue             # デフォルトレイアウト
  │   ├── feature-layout.vue      # 特定機能用レイアウト
  │   └── admin.vue               # 管理画面レイアウト
  ├── middleware/                 # Nuxt3ミドルウェア
  │   ├── auth.ts                 # 認証ミドルウェア
  │   ├── guest.ts                # ゲストミドルウェア
  │   └── admin.ts                # 管理者ミドルウェア
  ├── plugins/                    # Nuxt3プラグイン
  │   ├── pinia.client.ts         # Pinia初期化
  │   └── api.client.ts           # APIクライアント初期化
  ├── server/                     # サーバーサイド（ページ対応）
  │   ├── api/                    # APIエンドポイント
  │   │   ├── feature-a/          # 機能A API
  │   │   ├── feature-b/          # 機能B API
  │   │   ├── feature-c/          # 機能C API
  │   │   ├── feature-d/          # 機能D API
  │   │   └── feature-e/          # 機能E API
  │   └── middleware/             # サーバーミドルウェア
  ├── assets/                     # ビルド対象アセット
  │   ├── css/                    # CSS/SCSS
  │   ├── images/                 # 画像ファイル
  │   └── icons/                  # アイコンファイル
  ├── public/                     # 静的ファイル
  │   ├── favicon.ico
  │   └── robots.txt
  └── __test__/                    # テストファイル
      ├── components/             # コンポーネントテスト
      ├── composables/            # Composableテスト
      └── pages/                  # ページテスト
```

### ドメイン別詳細構造

#### UI コンポーネント層

```
components/ui/
├── Button.vue                 # ボタンコンポーネント
├── Input.vue                  # 入力フィールド
├── Modal.vue                  # モーダルダイアログ
├── Card.vue                   # カードコンポーネント
├── Loading.vue                # ローディング表示
├── Pagination.vue             # ページネーション
└── Toast.vue                  # 通知メッセージ
```

#### レイアウト層

```
components/layout/
├── Header.vue                 # ヘッダー
├── Footer.vue                 # フッター
├── Sidebar.vue                # サイドバー
├── Navigation.vue             # ナビゲーション
└── Breadcrumb.vue             # パンくずリスト
```

#### 機能 A ドメイン（Index.vue 起点）- 例：認証系機能

```
components/feature-a/
├── Index.vue                  # 🎯 機能Aメインテンプレート（ページ対応）
├── FormComponentA.vue         # 例：ログインフォーム
├── FormComponentB.vue         # 例：会員登録フォーム
├── FormComponentC.vue         # 例：パスワードリセット
├── ProviderComponent.vue      # 例：認証プロバイダー
├── ValidationComponent.vue    # 例：バリデーション表示
└── SocialComponent.vue        # 例：ソーシャル連携

composables/feature-a/
├── useFeatureA.ts             # 機能A状態管理
├── useFormA.ts                # フォームA処理
├── useFormB.ts                # フォームB処理
└── useValidation.ts           # バリデーション処理

pages/feature-a/
├── form-a.vue                 # フォームAページ
├── form-b.vue                 # フォームBページ
├── form-c.vue                 # フォームCページ
└── verify.vue                 # 検証ページ
```

#### 機能 B ドメイン（Index.vue 起点）- 例：エンティティ管理

```
components/feature-b/
├── Index.vue                  # 🎯 機能Bメインテンプレート（ページ対応）
├── ItemCard.vue               # アイテムカード
├── ItemList.vue               # アイテムリスト
├── ItemDetail.vue             # アイテム詳細
├── SearchComponent.vue        # 検索機能
├── FilterComponent.vue        # フィルター機能
├── GalleryComponent.vue       # ギャラリー表示
├── ReviewComponent.vue        # レビュー機能
├── ComparisonComponent.vue    # 比較機能
└── ActionButton.vue           # アクションボタン

composables/feature-b/
├── useFeatureB.ts             # 機能Bデータ管理
├── useItemDetail.ts           # アイテム詳細取得
├── useSearch.ts               # 検索機能
├── useFilter.ts               # フィルタリング
└── useReviews.ts              # レビュー管理

pages/feature-b/
├── index.vue                  # アイテム一覧
├── [id].vue                   # アイテム詳細
├── search.vue                 # 検索結果
└── category/
    └── [slug].vue             # カテゴリ別一覧
```

#### 機能 C ドメイン（Index.vue 起点）- 例：状態管理系機能

```
components/feature-c/
├── Index.vue                  # 🎯 機能Cメインテンプレート（ページ対応）
├── StateItem.vue              # 状態アイテム
├── StateList.vue              # 状態リスト
├── StateSummary.vue           # 状態サマリー
├── ProcessForm.vue            # 処理フォーム
├── MethodSelector.vue         # 方法選択
└── ConfigForm.vue             # 設定フォーム

composables/feature-c/
├── useFeatureC.ts             # 機能C状態管理
├── useProcess.ts              # 処理ロジック
└── useConfig.ts               # 設定管理

pages/feature-c/
├── index.vue                  # 状態確認
└── process.vue                # 処理実行
```

## 基本ルール

### 1. ページドメイン対応ルール

**必須対応関係**

- 各`pages/`ディレクトリに対応する`components/`ディレクトリを作成
- ページで使用するコンポーネントは対応するディレクトリに配置
- ページ固有のロジックは対応する`composables/`ディレクトリに配置

**対応例**

```
pages/feature-a/index.vue      → components/feature-a/Index.vue
pages/feature-b/process.vue    → components/feature-b/Index.vue
pages/feature-c/edit.vue       → components/feature-c/Index.vue
```

**Index.vue を起点とした構造例**

```
pages/feature-a/form.vue
  ↓ uses
components/feature-a/Index.vue
  ↓ composes
├── components/feature-a/FormComponent.vue
├── components/feature-a/ValidationComponent.vue
└── components/ui/Button.vue
```

**再利用可能な子コンポーネント例**

```
components/feature-b/ItemCard.vue
  ↓ 使用される場所
├── components/feature-b/Index.vue（アイテム一覧）
├── components/home/Index.vue（おすすめアイテム）
└── components/feature-c/StateItem.vue（アイテム情報表示）
```

### 2. コンポーネント分類ルール

**UI コンポーネント（`components/ui/`）**

- 他のコンポーネントに依存しない基本部品
- 再利用可能で汎用的な機能
- ビジネスロジックを含まない

**ドメインコンポーネント（`components/[domain]/`）**

- 特定の機能ドメインに属するコンポーネント
- `Index.vue`がページ対応のメインテンプレート
- 他のコンポーネントは機能別の子コンポーネント
- UI コンポーネントと同一ドメインコンポーネントを使用可能

**レイアウトコンポーネント（`components/layout/`）**

- ページ全体の構造を定義
- ヘッダー、フッター、サイドバーなど
- 複数ページで共有される構造部品

### Index.vue の役割

**各ドメインの`Index.vue`**

- 対応するページの主要テンプレート
- 同一ドメインの子コンポーネントを組み合わせ
- ページ固有のレイアウトやロジック統合
- 他ドメインのコンポーネントも必要に応じて使用

### 3. ファイル配置ルール

**単一ファイルコンポーネント**

- 基本的に 1 コンポーネント 1 ファイル
- ファイル名はコンポーネント名と一致

**複雑なコンポーネント**

- サブコンポーネントが多い場合はディレクトリで整理
- メインコンポーネントは`index.vue`とする

**型定義ファイル**

- コンポーネント固有の型は同一ディレクトリに配置
- 共通型は`types/`ディレクトリに配置

## 命名規則

### ファイル・ディレクトリ命名

**ディレクトリ名**

- 小文字（`feature-a`, `feature-b`, `feature-c`）
- ハイフン区切りを推奨
- 機能を表す明確な名前

**Vue コンポーネントファイル**

- PascalCase（`ItemCard.vue`, `FormComponent.vue`）
- 機能を表す明確な名前
- ドメインプレフィックス推奨（`ItemCard` vs `Card`）

**TypeScript ファイル**

- camelCase（`useFeatureA.ts`, `formatData.ts`）
- 機能を表すプレフィックス（`use-`, `format-`, `validate-`）

**型定義**

- PascalCase interface（`User`, `Item`, `StateData`）
- 明確で具体的な名前

### インポートエイリアス例

**ドメイン別エイリアス**

```typescript
// UI層
import Button from '@ui/Button.vue';

// レイアウト層
import Header from '@layout/Header.vue';

// ドメイン層（Index.vue - ページ対応）
import FeatureAIndex from '@feature-a/Index.vue';
import FeatureBIndex from '@feature-b/Index.vue';

// ドメイン層（子コンポーネント）
import FormComponent from '@feature-a/FormComponent.vue';
import ItemCard from '@feature-b/ItemCard.vue';
import StateItem from '@feature-c/StateItem.vue';

// ユーティリティ
import { formatData } from '@utils/format';

// 型定義
import type { User } from '@types/user';
```

**相対パス使用ケース**

- 同一ディレクトリ内: `./ComponentName.vue`
- 親ディレクトリ: `../ParentComponent.vue`
- 深い階層の場合のみエイリアス使用

## 依存関係ルール

### 許可される依存関係

**UI コンポーネント**

- ✅ 他の UI コンポーネント
- ❌ ドメインコンポーネント
- ❌ ページ固有ロジック

**ドメインコンポーネント**

- ✅ UI コンポーネント
- ✅ 同一ドメインコンポーネント
- ✅ 他ドメインコンポーネント（必要に応じて）
- ✅ 対応する Composables

**ページコンポーネント**

- ✅ ドメインコンポーネント（主に Index.vue）
- ✅ レイアウトコンポーネント
- ✅ 対応する Composables
- ✅ 対応する Stores

### 依存関係の方向

```
UI Components
    ↑
Domain Components (子コンポーネント)
    ↑
Domain Components (Index.vue)
    ↑
Page Components
```

**循環依存の禁止**

- ドメイン間での循環依存は避ける
- 共通ロジックは`composables/common/`に配置
- 共通コンポーネントは`components/ui/`に配置
- Index.vue 間での直接的な依存は禁止

## 開発フロー

### 新機能開発手順

1. **ドメイン定義**

   - 機能ドメインを特定
   - 対応するページルートを確認

2. **ディレクトリ作成**

   ```bash
   mkdir src/components/[your-domain]
   mkdir src/composables/[your-domain]
   mkdir src/pages/[your-domain]
   ```

3. **型定義作成**

   ```bash
   touch src/types/[your-domain].ts
   ```

4. **基本コンポーネント作成**

   - UI コンポーネントから開始
   - ドメインの子コンポーネント実装
   - Index.vue でコンポーネント統合

5. **ロジック実装**

   - Composables 作成
   - Store 作成（必要に応じて）

6. **ページ実装**
   - ルート定義
   - メタデータ設定

### 修正・保守手順

1. **影響範囲特定**

   - 修正対象ドメインを確認
   - 依存関係を分析

2. **テスト実行**

   - 対象ドメインのテスト実行
   - 依存する他ドメインのテスト確認

3. **段階的修正**

   - UI コンポーネントから修正
   - ドメインの子コンポーネント修正
   - Index.vue 修正
   - ページレベル修正

4. **動作確認**
   - 対象機能の動作確認
   - 関連機能への影響確認

## プロジェクトへの適用

### カスタマイズ指針

**1. ドメイン名の決定**

```markdown
# このガイドの例 → あなたのプロジェクト

feature-a → auth（認証）
feature-b → products（商品）
feature-c → cart（カート）
feature-d → profile（プロフィール）
feature-e → admin（管理）
```

**2. 命名規則の調整**

- 組織の既存規約に合わせて調整可能
- PascalCase/camelCase の原則は維持推奨
- ディレクトリ名は統一性を重視

**3. 機能追加パターン**

```bash
# 新機能追加の標準手順
1. pages/[new-feature]/ を作成
2. components/[new-feature]/ を作成
3. composables/[new-feature]/ を作成（必要に応じて）
4. types/[new-feature].ts を作成（必要に応じて）
5. stores/[new-feature].ts を作成（必要に応じて）
```

### ドメイン設計のポイント

**機能の粒度設定**

```
# 適切な粒度例
pages/user-management/    → ユーザー関連機能全般
pages/content-creation/   → コンテンツ作成機能
pages/data-analytics/     → 分析・レポート機能
pages/system-settings/    → システム設定機能

# 細かすぎる例（避けるべき）
pages/login/             → 認証の一部のみ
pages/signup/            → 認証の一部のみ
pages/password-reset/    → 認証の一部のみ
```

**命名の一貫性**

```
# 良い例：動詞+名詞パターン
pages/manage-users/      → ユーザー管理
pages/create-content/    → コンテンツ作成
pages/view-analytics/    → 分析表示

# 良い例：名詞パターン
pages/users/             → ユーザー関連
pages/content/           → コンテンツ関連
pages/analytics/         → 分析関連
```

### スケールアップ時の拡張

**小規模（機能数：3-5）**

```
components/
├── ui/
├── layout/
├── feature-a/
├── feature-b/
└── feature-c/
```

**中規模（機能数：6-12）**

```
components/
├── ui/
├── layout/
├── core/          # 横断的な機能
├── feature-a/
├── feature-b/
├── feature-c/
├── feature-d/
├── feature-e/
└── feature-f/
```

**大規模（機能数：13+）**

```
components/
├── ui/
├── layout/
├── core/
├── modules/       # 機能群のグルーピング
│   ├── user-management/
│   ├── content-management/
│   └── analytics/
└── shared/        # 共有コンポーネント
```

### 注意事項とベストプラクティス

**1. 段階的な導入**

```bash
# フェーズ1：新機能から適用
pages/new-feature/       → 新しい機能のみ新アーキテクチャ
components/new-feature/  → 新コンポーネントのみ

# フェーズ2：関連機能の移行
pages/related-feature/   → 関連する既存機能を移行
components/related-feature/

# フェーズ3：全体移行
# 既存機能を段階的に新アーキテクチャに移行
```

**2. チーム合意とルール統一**

- 命名規則はプロジェクト開始時に明文化
- コードレビューでアーキテクチャ遵守を確認
- 新メンバーへのオンボーディング資料整備
- 定期的なアーキテクチャレビューの実施

**3. ドキュメント維持**

- プロジェクト固有のドメイン定義を明文化
- 追加ルールや例外事項の記録
- 新メンバー向けオンボーディング資料の整備
- 定期的なアーキテクチャレビューの実施

**4. パフォーマンス考慮事項**

- 大きなドメインは遅延ロード（Lazy Loading）を検討
- Index.vue で不要なコンポーネントの一括インポート避ける
- ドメイン横断の共通処理は適切にキャッシュ

**5. よくある問題と対策**

**問題：ドメイン境界が曖昧**

```
# 悪い例
pages/user-profile-edit/     # ユーザー？プロフィール？
components/user-profile-edit/

# 良い例
pages/user-management/       # ユーザー管理として統合
components/user-management/
├── ProfileEdit.vue          # プロフィール編集
├── AccountSettings.vue      # アカウント設定
```

**問題：循環依存の発生**

```
# 避けるべきパターン
components/feature-a/Index.vue → components/feature-b/SomeComponent.vue
components/feature-b/Index.vue → components/feature-a/OtherComponent.vue

# 解決策：共通コンポーネント化
components/ui/SharedComponent.vue ← 両方から利用
```

**問題：過度な抽象化**

```
# 悪い例：抽象化しすぎ
components/ui/GenericDataProcessor.vue

# 良い例：具体的で明確
components/ui/UserDataTable.vue
components/ui/ProductList.vue
```

この参考ガイドを基に、プロジェクトの規模や要件に応じてカスタマイズし、チーム全体で一貫したアーキテクチャを維持してください。
