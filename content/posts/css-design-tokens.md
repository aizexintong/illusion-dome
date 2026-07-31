---
title: "CSS自定义属性：幻梦的设计令牌系统"
date: 2026-04-23T11:00:00+08:00
description: "深入幻梦的CSS自定义属性体系——40+颜色变量、间距尺度、字体排版、阴影层级、过渡曲线，一套完整的设计令牌。"
tags: ["CSS", "设计令牌", "变量", "主题", "架构"]
categories: ["技术分享"]
image: "https://t.alcy.cc/ycy?random=20"
---

## 为什么需要设计令牌？

如果你在 SCSS 中硬编码颜色值 `#FDF8F5`，当需要支持深色模式时，你得在几百个地方找到它们并修改。

设计令牌（Design Tokens）是解决方案：把颜色、间距、字号等以命名变量的形式统一管理。

## 幻梦的令牌体系

```css
:root {
  /* ===== 断点 ===== */
  --xs: 0;
  --sm: 576px;
  --md: 768px;
  --lg: 992px;
  --xl: 1200px;
  --xxl: 1440px;

  /* ===== 间距尺度 ===== */
  --s1: 4px;   --s2: 8px;   --s3: 12px;
  --s4: 16px;  --s5: 20px;  --s6: 24px;
  --s7: 28px;  --s8: 32px;  --s9: 36px;
  --s10: 40px; ... --s24: 96px;

  /* ===== 字体 ===== */
  --ff-body: 'Inter', 'Noto Sans SC', sans-serif;
  --ff-code: 'JetBrains Mono', monospace;
  --fs-xs: 0.75rem;
  --fs-sm: 0.875rem;
  --fs-base: 1rem;
  --fs-5xl: 3rem;

  /* ===== 圆角 ===== */
  --br1: 8px;   --br2: 12px;  --br3: 16px;

  /* ===== 阴影 ===== */
  --sh1: 0 2px 8px rgba(0,0,0,0.08);
  --sh2: 0 4px 16px rgba(0,0,0,0.12);

  /* ===== 过渡 ===== */
  --tr1: 0.2s ease;
  --tr2: 0.3s ease;
  --tr3: 0.5s cubic-bezier(0.4, 0, 0.2, 1);
}
```

## 色彩令牌

最复杂的部分是色彩。幻梦定义了两套色彩体系（浅色/深色），但共用同一套语义变量名：

```css
/* 浅色模式下的语义映射 */
:root, [data-theme="light"] {
  --cbg1: #FDF8F5;    /* 页面背景 */
  --cbg2: rgba(255, 255, 255, 0.72);  /* 卡片背景 */
  --ctp: #2E2825;     /* 正文颜色 */
  --cp1: #8B6F5E;     /* 主色-最深 */
  --cp5: #D4A574;     /* 主色-中间 */
  --cp9: #F5E6D3;     /* 主色-最浅 */
}

/* 深色模式下的语义映射 */
[data-theme="dark"] {
  --cbg1: #0E0A08;
  --cbg2: rgba(30, 24, 20, 0.72);
  --ctp: #EDE0D8;
  --cp1: #1C1210;
  --cp5: #8B6F5E;
  --cp9: #FDF3EE;
}
```

关键设计：**不直接使用颜色值，始终使用语义变量。** 切换 `data-theme` 属性，全局颜色自动更新。

## 使用方式

```css
.card {
  background: var(--cbg2);
  color: var(--ctp);
  border: 1px solid var(--cbd);
  padding: var(--s6);
  border-radius: var(--br2);
  box-shadow: var(--sh1);
  transition: transform var(--tr2), box-shadow var(--tr2);
}
```

## 优势

1. **主题切换**：改一个属性，全局生效
2. **一致性**：所有卡片使用相同的圆角和阴影
3. **可维护性**：调整全局间距尺度，只需改一处
4. **开放性**：用户可以在站点覆盖任意 token

---

*好的设计系统就像好的厨房——所有的材料、工具都摆在固定位置。做菜的时候，你不会边切洋葱边找盐。*
