---
title: "从零搭建 Hugo 主题：幻梦的开发笔记"
date: 2026-04-15T16:00:00+08:00
lastmod: 2026-07-15T10:00:00+08:00
description: "记录幻梦主题的开发过程——Hugo 主题的项目结构、模板系统、数据驱动设计、资源管道和开发工作流。"
tags: ["Hugo", "主题开发", "Go Template", "SCSS", "TypeScript"]
categories: ["技术分享"]
image: "https://t.alcy.cc/ycy?random=33"
---

## 为什么自己写主题？

市面上不缺 Hugo 主题。但当我一个个试用过去，发现它们总是在某个地方不符合期待——要么配色太冷淡，要么动画太浮夸，要么配置太复杂。

与其在几十个主题中寻找"差不多"的那个，不如花一个周末自己写。结果一个周末变成了三个月。

这篇文章记录了这个过程中的关键决策和技术要点。

## 项目结构：Less is More

Hugo 主题的最小结构其实很简单：

```
themes/illusion/
├── layouts/          # 模板（必须）
│   ├── _default/
│   │   └── baseof.html   # 外壳模板
│   └── index.html        # 首页模板
├── static/           # 静态文件
└── theme.toml        # 主题描述文件
```

但在开发中逐渐扩展为完整架构：

```
themes/illusion/
├── assets/
│   ├── scss/main.scss    # 样式 → Hugo Dart Sass 编译
│   └── ts/main.ts        # 脚本 → Hugo js.Build 编译
├── data/theme.yaml       # 数据文件 → Hugo 自动挂载
├── i18n/zh-CN.yaml       # 翻译 → Hugo i18n 系统
└── archetypes/default.md # 文章模板
```

没有 `package.json`，没有 `node_modules`。所有的构建都通过 Hugo 内置管道完成。

## 模板系统：组件化思考

Hugo 使用 Go 的 `html/template` 引擎。初看语法很奇怪——`{{ .Title }}`、`{{ range .Pages }}`、`{{ partial "foo" . }}`——但习惯后会发现它强制了一种良好的架构：模板必须组件化。

### baseof.html：一切的外壳

`baseof.html` 是 Hugo 的布局基础，所有页面都包裹在其中：

```go-html-template
<!DOCTYPE html>
<html lang="{{ .Site.LanguageCode }}">
<head>
  {{ partial "_layout/head.html" . }}
</head>
<body>
  {{ partial "_layout/header.html" . }}
  <main>
    {{ block "main" . }}{{ end }}
  </main>
  {{ partial "_layout/footer.html" . }}
</body>
</html>
```

`{{ block "main" . }}` 是插槽，由具体页面模板填充。这相当于一个简单的模板继承机制。

### Partial 分层

幻梦的 partials 分为四个目录：

| 目录 | 职责 | 示例 |
|------|------|------|
| `_layout/` | 全局布局 | head, header, footer, menu |
| `_components/` | 可复用 UI | article-card, page-hero, sidebar |
| `_shortcodes/` | 短代码 | skills, link-card |
| `_utils/` | 工具函数 | relative-time |

这种分层让模板结构清晰。修改导航不需要翻开文章卡片的代码，反之亦然。

## 数据驱动：一个 YAML 管理全站

幻梦最大的设计决策之一是**数据文件集中化**。所有页面内容全部在 `data/theme.yaml` 中定义。

```yaml
# data/theme.yaml
home:
  hero:
    title: "爱则心痛"
    subtitles: ["技术开发者", "代码艺术家"]
about:
  intro:
    name: "爱则心痛 (azxt)"
    quote: "代码如诗，技术如画。"
```

模板中通过 `hugo.Data.theme` 访问，站点根目录同名文件自动覆盖主题默认值。

## 资源管道：CSS 和 JS 的现代化

### SCSS → CSS

```go-html-template
{{ $css := resources.Get "scss/main.scss" | toCSS | minify | fingerprint }}
<link rel="stylesheet" href="{{ $css.RelPermalink }}">
```

`toCSS` 使用 Hugo 内置的 Dart Sass（需要 extended 版本）。

### TypeScript → JavaScript

```go-html-template
{{ $js := resources.Get "ts/main.ts" | js.Build (dict "target" "es2015" "format" "iife") | minify }}
<script src="{{ $js.RelPermalink }}" defer></script>
```

Hugo 的 `js.Build` 使用 esbuild，编译速度极快（通常 < 50ms）。

## 第三方库：CDN 为主，本地兜底

幻梦依赖三个库：AOS.js、Typed.js、Font Awesome 6。加载策略是"CDN 优先 + 本地兜底"——CDN 加载失败时自动回退到 `static/lib/` 下的本地副本。

## 开发工作流

```bash
hugo server --disableFastRender   # 开发
hugo --minify --cleanDestinationDir  # 构建
hugo server -D                    # 预览草稿
```

`hugo server` 自带 LiveReload，加上毫秒级的构建速度，开发体验非常流畅。

---

*写主题最深的体会是：好的框架让你专注于设计本身，而不是配置工具链。Hugo 在这方面做到了极致。*
