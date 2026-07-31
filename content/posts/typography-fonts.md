---
title: "字体排印：中英文混排的艺术"
date: 2026-04-27T15:00:00+08:00
description: "幻梦的字体系统——Inter+Noto Sans SC+JetBrains Mono的组合策略、系统字体回退、本地字体定义和性能权衡。"
tags: ["字体", "排版", "Typography", "设计", "中英文"]
categories: ["设计理念"]
image: "https://t.alcy.cc/ycy?random=25"
---

## 中英文混排是地狱难度

英文字体设计以拉丁字母为核心，中文字体是方块字。两者在视觉重量、字间距、行高上天然不同。把一份英文排版和一份中文排版放在一起，用同一套 CSS 规则，大概率会有一方不协调。

## 字体选择

### 正文：Inter + Noto Sans SC

```
font-family: "Inter", "Noto Sans SC", "PingFang SC", "Microsoft YaHei", sans-serif;
```

**Inter** 是为屏幕阅读优化的现代无衬线体，在 12-18px 的正文尺寸下清晰度极佳。

**Noto Sans SC**（思源黑体）是 Google 和 Adobe 联合开发的开源中文字体，覆盖完整的中文字符集，在低分辨率屏幕上也有良好的渲染效果。

### 代码：JetBrains Mono

```
font-family: "JetBrains Mono", "Fira Code", "Consolas", monospace;
```

JetBrains Mono 的特点：
- 连字支持（`=>` 显示为箭头）
- 更高的 x-height（小写字母更高，更易读）
- 代码专用的标点符号设计

### 装饰字体：站酷庆科黄油体

只在首页的引用文字中使用，增加视觉变化。

## 系统字体优先策略

`fonts.css` 使用 `local()` 优先加载系统已安装的字体：

```css
@font-face {
  font-family: 'Noto Sans SC';
  src: local('Noto Sans SC'),
       local('PingFang SC'),
       local('Microsoft YaHei'),
       url('/lib/fonts/NotoSansSC.woff2') format('woff2');
}
```

如果用户系统已安装了 PingFang SC 或微软雅黑，不需要下载任何字体文件。只有系统没有时才加载 Web 字体。

## 中英文间距

中英文混排时，两种文字之间需要一个微小间隙：

```css
body {
  /* 针对中英文混排的字间距 */
  text-spacing: auto;  /* 实验性属性 */
}
```

浏览器尚未完全支持自动添加间距，所以幻梦在模板中用零宽空格做了手动处理。

## 行高与字号

```css
body {
  font-size: 16px;
  line-height: 1.75;     /* 中文需要更大的行高 */
}

code {
  font-size: 0.875em;
  line-height: 1.5;      /* 代码行高略小 */
}
```

中文的行高一般需要 1.6-1.8，英文 1.4-1.6。正文行高 1.75 是一个中英文兼顾的折中值。

## 性能考虑

中文字体文件很大（Noto Sans SC 的完整包超过 20MB）。幻梦的策略：
1. 优先使用系统字体（零下载）
2. 只保留常规字重的 woff2 子集
3. 使用 `font-display: swap` 避免 FOIT（不可见文本闪烁）

---

*字体选择是博客的隐形设计。读者可能说不出正文是什么字体，但换成 Comic Sans 他们一定会跳起来。*
