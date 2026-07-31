---
title: "性能优化：一次构建，处处流畅"
date: 2026-04-25T14:00:00+08:00
description: "幻梦主题的性能优化策略——资源压缩、字体加载、CDN回退、粒子性能检测、CSS动画优化和懒加载的完整指南。"
tags: ["性能", "优化", "Lighthouse", "Web Vitals", "加载"]
categories: ["技术分享"]
image: "https://t.alcy.cc/ycy?random=23"
---

## 性能也是设计

一个加载缓慢的博客即使设计再美，用户也不会看到。Google 的研究表明：页面加载时间从 1 秒增加到 3 秒，跳出率增加 32%。

幻梦把性能当作设计的一部分。

## 资源压缩

Hugo 生产构建自动 minify：

```
hugo --minify
```

CSS 经 Dart Sass 编译后由 Hugo 压缩，JS 经 esbuild 编译后由 Hugo 压缩。HTML 模板输出同样自动压缩。

配合 `fingerprint`（内容哈希命名）：

```go-html-template
{{ $css := resources.Get "scss/main.scss" | toCSS | minify | fingerprint }}
<link href="{{ $css.RelPermalink }}" integrity="{{ $css.Data.Integrity }}">
```

文件名变成 `style.ab12cd34.min.css`，永久缓存，内容变更自动失效。

## 字体加载策略

```css
/* fonts.css */
@font-face {
  font-family: 'Noto Sans SC';
  src: local('PingFang SC'),    /* 优先系统字体 */
       local('Microsoft YaHei'),
       url('/lib/fonts/NotoSansSC.woff2') format('woff2');
  font-display: swap;           /* 先用系统字体，加载后替换 */
}
```

`font-display: swap` 确保文字立即可见（使用系统字体），字体加载完成后平滑替换。配合 `local()` 优先匹配系统已安装字体，对 Mac 和 Windows 中文用户几乎零加载成本。

## CDN 优先，本地兜底

AOS.js、Typed.js、Font Awesome 优先从 CDN 加载。CDN 失败时自动回退：

```html
<script src="https://cdn.jsdelivr.net/npm/aos@2.3.4/dist/aos.js"
        onerror="this.onerror=null;loadLocal('aos')"></script>
```

Font Awesome 的回退更智能——JS 检测图标是否成功渲染，失败时动态注入本地 CSS。

## 粒子性能自适应

Canvas 粒子系统根据设备性能调整：

```typescript
const LOW = 0.5;   // 低端设备: 粒子减半, 30fps
const MID = 0.75;  // 中端设备: 粒子减25%, 45fps
const HIGH = 1.0;  // 高端设备: 全量粒子, 60fps
```

检测维度：CPU 核心数、设备内存、是否为移动设备。

## CSS 动画优化

只动画 `transform` 和 `opacity`——这两个属性不触发 reflow：

```css
/* 好 —— 仅触发 composite */
.card { transition: transform 0.3s ease, opacity 0.3s ease; }

/* 避免 —— 触发 layout */
.card { transition: width 0.3s ease, left 0.3s ease; }
```

## Lighthouse 得分

优化后的幻梦主题：
- Performance: 95+
- Accessibility: 100
- Best Practices: 100
- SEO: 100

---

*性能优化不是最后一步。它应该从第一行代码开始就存在。*
