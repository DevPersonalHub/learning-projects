# Unicopi UI プロジェクト概要

## プロジェクト概要

Unicopi UIは、高度なツールチップシステムを実装したReact/Next.jsアプリケーションです。特に期間ベースの制御機能を持つツールチップシステムが特徴的で、日付範囲に基づいてコンテンツの表示を動的に制御できます。

## 技術スタック

### フロントエンド
- **Framework**: React + Next.js
- **Language**: JavaScript (ES6+)
- **Styling**: CSS Modules / CSS-in-JS
- **State Management**: React Context API

### ツールチップシステム
- **Configuration**: JSON-based configuration system
- **Date Handling**: Native JavaScript Date API
- **Validation**: カスタムバリデーション機能
- **Debugging**: 開発時デバッグ支援機能

## アーキテクチャパターン

### 1. Provider Pattern
```javascript
// TooltipProvider.jsx - 中央集約型のツールチップ管理
const TooltipProvider = ({ children }) => {
  // ツールチップの状態管理と表示ロジック
}
```

### 2. Utility Function Pattern
```javascript
// tooltipDateUtils.js - 日付処理の分離
export function parseDate(dateInput)
export function shouldDisplayTooltip(config)
export function validateDatePeriod(config)
```

### 3. Configuration-Driven Design
```json
// tooltips-sample.json - 設定駆動の実装
{
  "selector": "button",
  "startDate": "2025-01-01T00:00:00Z",
  "endDate": "2025-12-31T23:59:59Z",
  "contents": {"ja": "ツールチップ内容"}
}
```

## 主要コンポーネント

### 1. TooltipProvider.jsx
- **役割**: ツールチップ表示の中央制御
- **機能**: 
  - 期間フィルタリング
  - ページベースフィルタリング
  - エラーハンドリング
  - デバッグログ出力

### 2. tooltipDateUtils.js
- **役割**: 日付処理のユーティリティ
- **機能**:
  - 複数日付形式対応（ISO 8601, Unix timestamp, Date string）
  - 期間内判定
  - 設定バリデーション
  - エラーハンドリング

### 3. Configuration System
- **形式**: JSON設定ファイル
- **特徴**:
  - セレクタベースの要素指定
  - 期間指定（開始日・終了日）
  - 国際化対応（contents.ja）
  - コメント機能

## 期間制御機能の特徴

### 1. 柔軟な日付フォーマット
- **ISO 8601**: `"2025-01-01T00:00:00Z"`
- **簡単な日付**: `"2025-01-01"`
- **Unixタイムスタンプ**: `1704067200000`

### 2. バリデーション機能
- 日付形式の検証
- 論理的整合性チェック（開始日 < 終了日）
- エラー時のフォールバック処理

### 3. 表示パターン
- **期間限定**: 開始日〜終了日
- **開始日のみ**: 指定日以降に表示
- **終了日のみ**: 指定日まで表示
- **期間指定なし**: 常時表示

## 実装の技術的ポイント

### 1. パフォーマンス最適化
- 日付パースの最適化（一度だけ実行）
- 無効な設定の早期除外
- 効率的なフィルタリングパイプライン

### 2. エラーハンドリング戦略
- 無効な設定の検出と除外
- コンソール警告による開発支援
- グレースフルデグラデーション

### 3. デバッグ機能
- 詳細なコンソールログ
- フィルタリング統計の表示
- 期間チェック結果の表示

## 使用例とユースケース

### 1. キャンペーン告知
```json
{
  "selector": ".campaign-button",
  "contents": {"ja": "期間限定キャンペーン実施中！"},
  "startDate": "2025-01-01T00:00:00Z",
  "endDate": "2025-01-31T23:59:59Z"
}
```

### 2. 新機能の案内
```json
{
  "selector": ".new-feature",
  "contents": {"ja": "新機能をお試しください"},
  "startDate": "2025-01-15T00:00:00Z"
}
```

### 3. メンテナンス通知
```json
{
  "selector": ".maintenance-notice",
  "contents": {"ja": "メンテナンス中です"},
  "endDate": "2025-01-10T12:00:00Z"
}
```

## 今後の拡張可能性

### 1. タイムゾーン対応
- ユーザーの地域設定を考慮
- UTCとローカル時間の変換

### 2. 複数期間対応
- 週単位の繰り返し
- 曜日指定機能
- 時間帯指定

### 3. A/Bテスト機能
- 確率ベースの表示制御
- ユーザーグループ別の表示

### 4. 管理インターフェース
- 期間設定のGUI
- リアルタイムプレビュー
- 設定の一括管理

## 学習価値

このプロジェクトから学べる主要な技術概念：

1. **React/Next.js のベストプラクティス**
   - Provider Pattern の活用
   - useEffect の適切な使用
   - 依存配列の管理

2. **JavaScript Date API の活用**
   - 複数フォーマットの日付処理
   - タイムゾーンの考慮
   - エラーハンドリング

3. **設定駆動開発**
   - JSON設定の柔軟性
   - バリデーションの重要性
   - デバッグ情報の提供

4. **アーキテクチャ設計**
   - 関心の分離
   - 単一責任の原則
   - 拡張性の確保

このプロジェクトは、実用的な機能実装を通じて、モダンなReact開発の様々な側面を学ぶことができる優れた例となっています。