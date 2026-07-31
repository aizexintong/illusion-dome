---
title: "无障碍设计：不是为了合规，是为了人"
date: 2026-04-16T14:00:00+08:00
description: "幻梦的无障碍实现——跳过链接、ARIA标签、焦点管理、键盘导航、颜色对比度和prefers-reduced-motion支持。"
tags: ["无障碍", "A11y", "ARIA", "键盘导航", "用户体验"]
categories: ["设计理念"]
image: "https://t.alcy.cc/ycy?random=13"
---

## A11y 不是检查清单

很多开发者对待无障碍的态度是：通过 Lighthouse 检测，然后打勾。这不是无障碍设计（Accessibility），这是无障碍合规（Compliance）。

幻梦的目标是：**让使用屏幕阅读器的人、只用键盘的人、对动画敏感的人，也能享受这个博客。**

## 跳过链接

键盘用户最痛苦的体验是按无数次 Tab 才能跳过导航栏到达内容。跳过链接解决这个问题：

```html
<a href="#main-content" class="skip-link">跳转到内容</a>
```

```css
.skip-link {
  position: absolute;
  top: -100%;
  left: 0;
}

.skip-link:focus {
  top: 0;
  z-index: 9999;
  background: var(--cp5);
  color: white;
  padding: 0.5rem 1rem;
}
```

平时隐藏，Tab 聚焦时滑入。这是给键盘用户的第一份礼物。

## ARIA 标签

导航栏、搜索按钮、主题切换按钮都标记了 `aria-label`：

```html
<button aria-label="切换主题模式" title="切换主题">
  <i class="fas fa-moon"></i>
</button>

<button aria-label="打开搜索" title="搜索 (Ctrl+K)">
  <i class="fas fa-search"></i>
</button>
```

屏幕阅读器会读出这些标签。没有 aria-label，用户听到的是"按钮"——不知道这个按钮做什么。

## 焦点管理

移动端菜单打开时，焦点自动移到第一个菜单项；关闭时，焦点回到菜单按钮：

```typescript
openMobileMenu(): void {
  this.menu.classList.add('active');
  const firstLink = this.menu.querySelector('a');
  firstLink?.focus();
}

closeMobileMenu(): void {
  this.menu.classList.remove('active');
  this.menuButton.focus();  // 焦点回到触发按钮
}
```

搜索模态框同理。

## 键盘导航

所有交互元素都可以通过键盘操作。菜单项、卡片链接、分页按钮都响应 Enter 和 Space 键。搜索框支持 Escape 关闭。

## 颜色对比度

```css
:root {
  --ctp: #2E2825;   /* 正文颜色 —— 对浅色背景对比度 12:1 */
}

[data-theme="dark"] {
  --ctp: #EDE0D8;   /* 正文颜色 —— 对深色背景对比度 10:1 */
}
```

WCAG AA 标准要求 4.5:1，AAA 要求 7:1。幻梦的正文对比度在两种模式下都超过 10:1。

## provides-reduced-motion

```css
@media (prefers-reduced-motion: reduce) {
  .particle-canvas { display: none; }
  [data-aos] { opacity: 1 !important; transform: none !important; }
}
```

粒子动画和 AOS 动画对有前庭障碍的用户可能引发眩晕。完全禁用。

---

*无障碍不是额外的功能。它是设计的一部分，正如斜坡和楼梯都是建筑的一部分。*
