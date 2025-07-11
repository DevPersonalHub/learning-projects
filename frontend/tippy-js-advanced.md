Tippy.js 高度な活用法

  カスタムテーマ作成

  .customTooltip {
    background-color: rgba(40, 40, 40, 0.95) !important;
    backdrop-filter: blur(8px) !important;
    // 高い特異性で確実に適用
  }

  動的背景色設定

  onCreate(instance) {
    if (config.backgroundColor) {
      const tooltip = instance.popper.querySelector('.tippy-tooltip');
      tooltip.style.backgroundColor = config.backgroundColor;
    }
  }

  オフセット計算

  // Tippy.js format: [skidding, distance]
  offset: [offsetX, offsetY]
  EOF
