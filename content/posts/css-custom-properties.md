---
title: "CSS自定义属性：幻梦的动态色彩引擎"
date: 2026-04-26T10:00:00+08:00
description: "深入幻梦的CSS自定义属性系统——如何用40多个变量管理双主题色彩、为什么用语义变量替代原始色值、以及运行时动态切换的实现。"
tags: ["CSS", "自定义属性", "变量", "色彩", "主题"]
categories: ["技术分享"]
image: "https://t.alcy.cc/ycy?random=24"
---

## 没有变量的 CSS 是死的

在 CSS 自定义属性（`--var`）出现之前，主题切换的唯一方式是用两套 CSS 文件或 JavaScript 动态替换 `class`。前者维护困难，后者性能差。

CSS 自定义属性改变了游戏规则。

## 幻梦的变量层级

三层结构：**原始色阶 → 语义变量 → 组件变量**

### 第一层：原始色阶

```css
:root {
  --p1: #2E2825; --p2: #3D3632; /* ... */ --p9: #FFFBF8;
  --s1: #343026; --s2: #4A4337; /* ... */ --s9: #FEF9F0;
  --a1: #1F4030; --a2: #2A523E; /* ... */ --a9: #EEF7F0;
  --g1: #3D2E10; --g2: #5C4318; /* ... */ --g9: #FEF8E8;
}

[data-theme="dark"] {
  --p1: #FDF3EE; --p2: #F0DED0; /* ... */ --p9: #0E0A08;
  /* 暗色模式下色阶反转 */
}
```

4 组色阶（primary / secondary / accent / gold），每组 9 个梯度。

### 第二层：语义变量

```css
--ctp:  var(--p1);    /* 正文颜色 — 永远是色阶最深色 */
--ctsg: var(--p2);    /* 次要文字 — 永远是色阶次深色 */
--cbg1: var(--p9);    /* 页面背景 — 永远是色阶最浅色 */
--cbg2: var(--p8);    /* 卡片背景 */
--cbg3: var(--p7);    /* 更深一层背景 */
--cbd:  var(--p6);    /* 边框 */
--cpl:  var(--a5);    /* 链接颜色 — 永远是 accent 中间色 */
--csh1: var(--p1:20%); /* 阴影 */
```

关键原则：**组件只引用语义变量，永远不禁用原始色阶。**

### 第三层：组件变量

```css
.card {
  background: var(--cbg2);
  color: var(--ctp);
  border-color: var(--cbd);
}

.card:hover {
  background: var(--cbg3);
}

[data-theme="dark"] .card {
  --card-shadow: var(--csh2);
}
```

## 动态切换

主题切换只改一个属性：

```typescript
document.documentElement.setAttribute('data-theme', 'dark');
```

所有引用语义变量的元素自动切换颜色。不需要重新计算样式，不需要刷新页面。

## 为什么不用 Sass 变量？

Sass 变量在编译时解析，运行时无法改变。CSS 自定义属性在运行时解析，可以通过 JS 动态修改。

```scss
// Sass — 编译时固定，无法运行时切换
$text-color: #2E2825;

// CSS 自定义属性 — 运行时可变
--ctp: #2E2825;
```

幻梦用 Sass 变量管理设计 Token（字体大小、间距、断点——这些不需要运行时改变），用 CSS 自定义属性管理色彩（需要运行时切换）。

## 浏览器兼容性

CSS 自定义属性的浏览器支持率超过 96%（2026年）。IE 11 已在 2022 年停止支持，不需要考虑兼容。

---

*CSS 自定义属性是 Web 设计史上最被低估的进步之一。它让 CSS 从静态走向动态。*
