# ツールチップ期間制御機能

## 機能概要

ツールチップの表示期間を制御する機能です。特定の日付範囲でのみツールチップを表示できるため、キャンペーン告知や期間限定のお知らせなどに活用できます。

## 技術的な実装

### 主要ファイル

1. **`src/utils/tooltipDateUtils.js`** - 日付処理ユーティリティ
2. **`src/components/TooltipProvider.jsx`** - メインプロバイダー
3. **`public/tooltips-sample.json`** - 設定ファイル例

### コア関数

```javascript
// 日付パース（複数フォーマット対応）
export function parseDate(dateInput) {
  if (!dateInput) return null;
  
  if (typeof dateInput === 'number') {
    return new Date(dateInput);
  }
  
  if (typeof dateInput === 'string') {
    const date = new Date(dateInput);
    if (isNaN(date.getTime())) {
      throw new Error(`Invalid date format: ${dateInput}`);
    }
    return date;
  }
  
  throw new Error(`Unsupported date type: ${typeof dateInput}`);
}

// 期間内判定
export function isWithinPeriod(start, end) {
  const now = getCurrentDate();
  
  if (start && now < start) return false;
  if (end && now > end) return false;
  
  return true;
}

// 表示判定メイン関数
export function shouldDisplayTooltip(tooltipConfig) {
  const { startDate, endDate } = tooltipConfig;
  
  if (!startDate && !endDate) {
    return true;
  }
  
  try {
    const start = startDate ? parseDate(startDate) : null;
    const end = endDate ? parseDate(endDate) : null;
    
    return isWithinPeriod(start, end);
  } catch (error) {
    console.error('Error checking tooltip period:', error);
    return false;
  }
}
```

### バリデーション機能

```javascript
export function validateDatePeriod(tooltipConfig) {
  const { startDate, endDate } = tooltipConfig;
  
  try {
    const start = startDate ? parseDate(startDate) : null;
    const end = endDate ? parseDate(endDate) : null;
    
    // 開始日が終了日より後の場合はエラー
    if (start && end && start > end) {
      return {
        isValid: false,
        message: 'Start date cannot be after end date'
      };
    }
    
    return { isValid: true, message: 'Valid date period' };
  } catch (error) {
    return {
      isValid: false,
      message: error.message
    };
  }
}
```

## 対応する日付フォーマット

### 1. ISO 8601 形式
```json
{
  "startDate": "2025-01-01T00:00:00Z",
  "endDate": "2025-12-31T23:59:59Z"
}
```

### 2. 簡単な日付形式
```json
{
  "startDate": "2025-01-01",
  "endDate": "2025-12-31"
}
```

### 3. Unix タイムスタンプ
```json
{
  "startDate": 1704067200000,
  "endDate": 1735689599000
}
```

## 使用パターン

### 1. 期間限定キャンペーン
```json
{
  "selector": ".campaign-button",
  "contents": {"ja": "期間限定キャンペーン実施中！"},
  "startDate": "2025-01-01T00:00:00Z",
  "endDate": "2025-01-31T23:59:59Z",
  "comment": "1月限定キャンペーン"
}
```

### 2. 新機能案内（開始日のみ）
```json
{
  "selector": ".new-feature",
  "contents": {"ja": "新機能をお試しください"},
  "startDate": "2025-01-15T00:00:00Z",
  "comment": "1/15から新機能リリース"
}
```

### 3. メンテナンス通知（終了日のみ）
```json
{
  "selector": ".maintenance-notice",
  "contents": {"ja": "メンテナンス中です"},
  "endDate": "2025-01-10T12:00:00Z",
  "comment": "1/10 12:00までメンテナンス"
}
```

## TooltipProvider での実装

```javascript
const TooltipProvider = ({ children }) => {
  useEffect(() => {
    // ページに該当するツールチップを取得
    const pageTooltips = tooltips.filter(tooltip => 
      tooltip.page === currentPage
    );
    
    // 期間フィルタリング
    const activeTooltips = pageTooltips.filter(tooltip => {
      const validation = validateDatePeriod(tooltip);
      if (!validation.isValid) {
        console.warn(`Invalid date period: ${validation.message}`);
        return false;
      }
      return shouldDisplayTooltip(tooltip);
    });
    
    // デバッグ情報
    console.log(`Tooltip filtering summary:`, {
      total: tooltips.length,
      afterPageFilter: pageTooltips.length,
      afterDateFilter: activeTooltips.length,
      filteredOutByDate: pageTooltips.length - activeTooltips.length
    });
    
    // アクティブなツールチップのみを表示
    activeTooltips.forEach(tooltip => {
      // ツールチップの表示処理
    });
  }, [currentPage, tooltips]);
};
```

## エラーハンドリング

### 1. 無効な日付形式
```javascript
// 無効な日付が指定された場合
{
  "startDate": "invalid-date",
  "endDate": "2025-12-31"
}
// → コンソールに警告を出力し、ツールチップを非表示
```

### 2. 論理的な矛盾
```javascript
// 開始日が終了日より後の場合
{
  "startDate": "2025-12-31",
  "endDate": "2025-01-01"
}
// → バリデーションエラーでツールチップを非表示
```

### 3. グレースフルデグラデーション
```javascript
// エラーが発生した場合の処理
try {
  return shouldDisplayTooltip(tooltip);
} catch (error) {
  console.error('Error checking tooltip period:', error);
  return false; // エラー時は安全に非表示
}
```

## デバッグ機能

### 1. 詳細ログ出力
```javascript
if (tooltip.startDate || tooltip.endDate) {
  console.log(`Tooltip ${tooltip.selector} - Period check:`, {
    startDate: tooltip.startDate,
    endDate: tooltip.endDate,
    shouldDisplay: shouldDisplay,
    validation: validation.message
  });
}
```

### 2. フィルタリング統計
```javascript
console.log(`Tooltip filtering summary:`, {
  total: totalTooltips,
  afterPageFilter: pageFilteredTooltips,
  afterDateFilter: dateFilteredTooltips,
  filteredOutByDate: pageFilteredTooltips - dateFilteredTooltips
});
```

## 学習ポイント

### 1. JavaScript Date API の活用
- 複数フォーマットの日付処理
- タイムゾーンの考慮
- 日付の比較と計算

### 2. エラーハンドリングパターン
- try-catch による例外処理
- バリデーション結果の構造化
- フォールバック処理

### 3. 設定駆動開発
- JSON設定による柔軟性
- バリデーション機能の重要性
- デバッグ情報の提供

### 4. React/Next.js パターン
- useEffect での非同期処理
- 依存配列の適切な管理
- コンポーネント間の状態管理

## パフォーマンス最適化

### 1. 日付パースの最適化
- 一度だけ実行（メモ化）
- 無効な設定の早期除外
- 効率的なフィルタリング

### 2. メモリ効率
- 不要なオブジェクト生成の回避
- 適切なガベージコレクション
- リソースの適切な解放

## 今後の改善案

### 1. タイムゾーン対応
- ユーザーの地域設定を考慮
- UTCとローカル時間の変換
- 地域別の表示制御

### 2. 複数期間対応
- 週単位の繰り返し
- 曜日指定機能
- 時間帯指定

### 3. 条件分岐の拡張
- ユーザーグループ別表示
- A/Bテスト機能
- 確率ベースの表示制御

この機能実装を通じて、日付処理、エラーハンドリング、設定駆動開発など、実用的な開発スキルを深く学ぶことができました。