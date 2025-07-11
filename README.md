# My Development Knowledge Base

  個人的な開発学習記録とナレッジベース

  ## 構造
  - `frontend/` - フロントエンド技術の学習記録
  - `projects/` - プロジェクト別の学習ログ
  - `tools/` - ツール・ライブラリの使用方法
  - `troubleshooting/` - 問題解決の記録

  ## 最近の学習
  - ツールチップ機能拡張（Tippy.js、SCSS Modules）
  - 画像最適化とインライン表示
  - コンポーネント設計パターン

  ## 学習原則
  - 抽象化したパターンで記録
  - 再利用可能な知見を重視
  - 継続的な振り返りと更新

  projects/unicopi-tooltip-enhancement.md

  # ツールチップ機能拡張プロジェクト

  **日付**: 2024-07-10
  **技術**: React, Next.js, Tippy.js, SCSS Modules
  **概要**:
  ツールチップの背景設定、インライン画像、座標オフセット機能の実装

  ## 学習した技術

  ### Tippy.js 高度なカスタマイズ
  - カスタムテーマの作成と適用
  - 動的背景色設定のパターン
  - オフセット計算とポジショニング制御

  ### SCSS Modules の実践活用
  - コンポーネント固有のスタイル管理
  - CSS セレクターの特異性制御
  - インライン画像のサイズ制御テクニック

  ### 画像最適化のベストプラクティス
  - インライン vs ブロック画像の自動判別
  - レスポンシブ対応とパフォーマンス最適化
  - エラーハンドリングのUX配慮

  ## 実装パターンの発見

  ### ユーティリティ関数の分離設計
  ```javascript
  // 画像処理の汎用パターン
  function processImages(content, options) {
    // 1. パス解決とセキュリティチェック
    // 2. インライン/ブロック判定
    // 3. サイズ制御とレスポンシブ対応
  }

  // オフセット計算の汎用パターン
  function calculateOffset(config, element) {
    // 1. デバイス種別検出
    // 2. 画面境界チェック
    // 3. 配置方向に応じた調整
  }

  エラーハンドリングの学び

  - 過度なエラー表示はUXを損なう
  - ブラウザ標準動作を活用する重要性
  - ユーザーフレンドリーなフォールバック設計

  今後の活用予定

  - モーダルコンポーネントに画像処理ロジック適用
  - ドロップダウンメニューにオフセット機能追加
  - 他プロジェクトでユーティリティ関数再利用

  作成した技術資産

  - tooltipImageUtils.js - 汎用画像処理ライブラリ
  - tooltipOffsetUtils.js - 位置調整計算ライブラリ
  - Tooltip.module.scss - カスタムテーマ定義

  振り返り

  この機能拡張を通じて、サードパーティライブラリの効果的なカスタマイズ方法
  と、
  ユーザビリティを重視したエラーハンドリングの重要性を深く理解できた。

  ### **frontend/tippy-js-advanced.md**
  ```markdown
  # Tippy.js 高度な活用法

  ## カスタムテーマ作成のベストプラクティス

  ### SCSS Modulesでのテーマ定義
  ```scss
  .customTooltip {
    background-color: rgba(40, 40, 40, 0.95) !important;
    backdrop-filter: blur(8px) !important;
    border-radius: 8px !important;
    box-shadow: 0 4px 14px rgba(0, 0, 0, 0.15) !important;

    // 高い特異性(!important)で確実に適用
  }

  動的背景色設定

  // onCreate コールバックでの動的スタイル適用
  onCreate(instance) {
    if (config.backgroundColor) {
      const tooltip = instance.popper.querySelector('.tippy-tooltip, 
  .tippy-box');
      if (tooltip) {
        tooltip.style.backgroundColor = config.backgroundColor;
      }
    }
  }

  オフセット機能の実装

  Tippy.js オフセット形式

  // [skidding, distance] 形式
  offset: [offsetX, offsetY]

  // 配置方向に応じた調整例
  function adjustOffsetForPlacement(placement, x, y) {
    switch(placement) {
      case 'top': return [x, -Math.abs(y)];
      case 'bottom': return [x, Math.abs(y)];
      case 'left': return [-Math.abs(x), y];
      case 'right': return [Math.abs(x), y];
    }
  }

  パフォーマンス最適化

  画像プリロード

  async function preloadImages(htmlContent) {
    const imgUrls = extractImageUrls(htmlContent);
    const preloadPromises = imgUrls.map(url => {
      return new Promise((resolve) => {
        const img = new Image();
        img.onload = () => resolve(true);
        img.onerror = () => resolve(false);
        img.src = url;
      });
    });
    return Promise.all(preloadPromises);
  }

  学んだ設計原則

  - 設定の正規化パターン
  - 段階的フォールバック
  - ブラウザ標準動作の活用

  ### **tools/scss-modules.md**
  ```markdown
  # SCSS Modules 実践ガイド

  ## CSS特異性の制御

  ### インライン画像の強制スタイル適用
  ```scss
  // より具体的なセレクターで確実に適用
  [data-tippy-root] .tippy-content img[class*="inline"],
  .tippy-tooltip img[class*="inline"] {
    max-height: 16px !important;
    max-width: 16px !important;
    vertical-align: middle !important;
    display: inline !important;
    object-fit: contain !important;
  }

  モジュール化のメリット

  - スタイルの衝突回避
  - コンポーネント固有のスコープ
  - 保守性の向上

  レスポンシブ対応パターン

  @media (max-width: 768px) {
    .tooltipCustom {
      max-width: 280px !important;
      font-size: 13px !important;
      padding: 10px 14px !important;
    }
  }

  学習ポイント

  - !important の適切な使用タイミング
  - CSS Module のクラス名生成パターン
  - サードパーティライブラリとの共存方法
