---
title: "6000行SCSS：一个单体样式表的设计哲学"
date: 2026-04-23T10:00:00+08:00
description: "幻梦主题的SCSS架构——为什么选择单体文件、CSS自定义属性的层级设计、设计Token系统和可维护性策略。"
tags: ["SCSS", "CSS", "架构", "设计系统", "样式"]
categories: ["技术分享"]
image: "https://t.alcy.cc/ycy?random=20"
---

## 为什么要单体文件？

大多数前端项目推崇组件化 CSS——每个组件一个文件，通过 `@import` 或 `@use` 组织。但幻梦的 `main.scss` 是一个超过 6000 行的单体文件。

这是一个有意为之的选择。

## 单体文件的理由

1. **编译性能**：Hugo 的 Dart Sass 编译器处理一个文件比处理 30 个 `@use` 快得多。主题的样式编译在开发环境下 < 100ms。

2. **全局上下文**：CSS 自定义属性（变量）在所有组件间共享。当你在 6000 行代码中修改一个变量名时，单体文件的全局搜索替换比跨文件重构更安全。

3. **调试透明**：Hugo server 的热更新是文件级的。修改一个文件的某一行后，浏览器直接显示对应的行号——单体文件让源码映射更精准。

## 文件结构

虽然是单体文件，但内部有清晰的逻辑分区：

```
main.scss (~6200 lines)
├── 1. 设计Tokens (1-173行)
│   ├── 断点 (xs-xxl)
│   ├── 间距标度 (s1-s24)
│   ├── 字体 (家族、大小、字重、行高)
│   ├── 圆角、过渡、缓动、阴影
│   └── 容器宽度、图标尺寸
├── 2. 色彩系统 (174-400行)
│   ├── 浅色模式 — 暖桃色系
│   └── 深色模式 — 暖深色系
├── 3. 基础样式 (402+行)
│   ├── Reset, Typography, Links
│   ├── 滚动条、无障碍、工具类
│   └── 容器、布局、段落标题
├── 4. 组件系统
│   ├── 卡片系统
│   ├── 按钮系统
│   └── 表单元素
├── 5. 页面组件
│   ├── Header, Footer, Navigation
│   ├── Hero, About, Skills
│   ├── Posts, Archives, Tags
│   ├── Links, Tools, 404
│   └── Sidebar, Pagination, Search
├── 6. 文章内样式
│   └── Typography, Code, Table, Blockquote
└── 7. 动画 (Keyframes)
    └── ~20+ @keyframes
```

## 设计 Token 系统

变量命名遵循 `{类别}{子类}{等级}` 模式：

```scss
// 断点
$bp-xs: 0;
$bp-md: 768px;
$bp-xl: 1200px;

// 间距
$s1: 0.25rem;   // 4px
$s4: 1rem;      // 16px
$s8: 2rem;      // 32px
$s24: 6rem;     // 96px

// 字体
$ff-body: 'Inter', 'Noto Sans SC', sans-serif;
$ff-mono: 'JetBrains Mono', 'Consolas', monospace;
$fs-xs: 0.75rem;
$fs-2xl: 1.5rem;
$fs-5xl: 3rem;

// 圆角
$br1: 8px;
$br2: 16px;
$br-round: 50%;

// 过渡
$tf-fast: 0.15s ease;
$tf-base: 0.3s ease;
$tf-slow: 0.5s ease;
```

## 色彩的层级命名

```scss
/* 浅色模式 */
--cp1: #...; --cp2: #...; ... --cp9: #...;  /* primary */
--cs1: #...; --cs2: #...; ... --cs9: #...;  /* secondary */
--ca1: #...; --ca2: #...; ... --ca9: #...;  /* accent */
--cg1: #...; --cg2: #...; ... --cg9: #...;  /* gold */

/* 语义变量 */
--ctp: var(--cp1);    /* text primary */
--ctsg: var(--cp2);   /* text secondary */
--cbg1: var(--cp9);   /* background 1 */
--cbg2: var(--cp8);   /* background 2 (cards) */
--cbd: var(--cp6);    /* border */
```

浅色和深色模式使用相同的层级命名，但映射到不同的色值。组件代码中只引用语义变量（如 `--ctp`），永远不禁用原始色阶。

---

*单体文件不是反模式。当架构足够清晰时，一个文件可以比一个目录更容易维护。*
