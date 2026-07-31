---
title: "五级断点：幻梦的响应式设计策略"
date: 2026-04-09T10:00:00+08:00
description: "幻梦主题的响应式设计——五级断点、移动优先策略、以及在不同屏幕上如何重新组织信息层次。"
tags: ["响应式", "CSS", "断点", "移动端", "适配"]
categories: ["设计理念"]
image: "https://t.alcy.cc/ycy?random=7"
---

## 没有移动端的博客是不完整的

超过 60% 的博客流量来自移动设备。如果你的博客在手机上阅读体验糟糕，你正在失去大部分读者。

幻梦从设计之初就以移动端为基准——先做好小屏幕，再向大屏幕扩展。

## 五级断点

```
xs: < 576px    手机竖屏
sm: ≥ 576px    手机横屏 / 小平板
md: ≥ 768px    平板竖屏
lg: ≥ 992px    平板横屏 / 小笔记本
xl: ≥ 1200px   桌面显示器
xxl: ≥ 1440px  大屏显示器
```

配合 SCSS 的 mixin：

```scss
@mixin mobile {
  @media (max-width: 575px) { @content; }
}

@mixin tablet {
  @media (min-width: 768px) { @content; }
}

@mixin desktop {
  @media (min-width: 992px) { @content; }
}
```

## 每个断点发生什么？

**xs (手机)**：单列布局，导航转为汉堡菜单，卡片全宽，侧边栏移到正文下方，字号略小。

**sm (小平板)**：技能卡片、哲学网格开始双列排列。

**md (平板)**：桌面导航出现（移动端菜单隐藏），首页 Hero 转为双列，文章卡片双列，侧边栏出现。

**lg (笔记本)**：文章卡片三列，技能卡片三列，友链三列，首页标题更大。

**xl+ (桌面)**：容器宽度增加，四周留白更多，视觉更舒展。

## 移动端的特殊处理

### 导航

桌面端使用水平导航栏，移动端使用全屏覆盖式菜单：

```scss
.mobile-menu {
  position: fixed;
  top: 0;
  right: -100%;
  width: 280px;
  height: 100vh;
  transition: right 0.3s ease;
  backdrop-filter: blur(24px);

  &.active {
    right: 0;
  }
}
```

### 粒子特效

移动端自动减少 50% 粒子数量，节省电量和 GPU 资源。

### 图片

封面图在小屏幕上使用更激进的压缩和更小的尺寸。

## 字体适配

不同屏幕使用不同的基础字号：

```css
html { font-size: 15px; }                    /* 手机 */
@media (min-width: 768px) { html { font-size: 16px; } }   /* 平板/桌面 */
@media (min-width: 1200px) { html { font-size: 17px; } }  /* 大屏 */
```

使用 `rem` 单位而非 `px`，全局字号一条规则驱动。

---

*响应式不是"在手机上也能看"，而是"在每种屏幕上都是最好的体验"。*
