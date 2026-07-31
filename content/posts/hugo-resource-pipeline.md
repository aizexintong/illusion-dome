---
title: "Hugo资源管道：从SCSS到TypeScript的一键编译"
date: 2026-05-10T11:00:00+08:00
description: "深入Hugo的资源管道——toCSS、js.Build、资源处理、fingerprint、缓存策略和开发/生产环境的最佳实践。"
tags: ["Hugo", "资源管道", "SCSS", "TypeScript", "编译"]
categories: ["技术分享"]
image: "https://t.alcy.cc/ycy?random=39"
---

## 你以为你需要webpack

当你决定用 SCSS 和 TypeScript 来开发 Hugo 主题时，第一反应可能是"我需要 webpack"。

然后你开始配置 `webpack.config.js`——设置 Sass loader、TypeScript compiler、output 路径、source maps、HMR……200 行配置后你终于可以开始写代码了。

但其实 Hugo 已经内置了这一切。

## Hugo Pipes

Hugo Pipes 是 Hugo 的资源处理管道，通过 Go Template 语法串联：

```go-html-template
{{ $style := resources.Get "scss/main.scss"
  | resources.ToCSS
  | minify
  | fingerprint
}}
<link rel="stylesheet" href="{{ $style.RelPermalink }}" integrity="{{ $style.Data.Integrity }}" />
```

管道解读：
1. `resources.Get` — 从 `assets/` 目录读取源文件
2. `resources.ToCSS` — Dart Sass 编译（SCSS → CSS）
3. `minify` — 压缩
4. `fingerprint` — 内容哈希命名 + 完整性校验

零配置，零依赖。

## SCSS 编译

```bash
# 需要 Hugo Extended 版本（内置 Dart Sass）
hugo version
# hugo v0.146.0+extended ...
```

如果你的 Hugo 不是 extended 版本，`toCSS` 会报错。

Dart Sass 是 Hugo 唯一支持的 Sass 编译器。LibSass 已被弃用，Hugo 在 0.146 版本中彻底移除了对它的支持。

## TypeScript 编译

```go-html-template
{{ $js := resources.Get "ts/main.ts" | js.Build (dict
  "target" "es2015"
  "format" "iife"
  "minify" hugo.IsProduction
  "sourceMap" (cond hugo.IsProduction "" "inline")
) }}
```

`js.Build` 的关键参数：

| 参数 | 说明 |
|------|------|
| `target` | 编译目标: es2015 / es2016 / es2017 / ... / esnext |
| `format` | 输出格式: iife / esm / cjs |
| `minify` | 是否压缩 |
| `sourceMap` | source map 模式: inline / external / "" (禁用) |
| `defines` | 编译时替换常量 |

## 开发 vs 生产

```go-html-template
{{ if hugo.IsProduction }}
  {{ $js = $js | minify | fingerprint }}
{{ end }}

{{ if not hugo.IsProduction }}
  {{ $js = $js | js.Build (dict "sourceMap" "inline") }}
{{ end }}
```

开发环境（`hugo server`）：不压缩、带 source map、不 fingerprint。编译速度 < 100ms。

生产环境（`hugo --minify`）：压缩、fingerprint、无 source map。文件大小通常是开发环境的 30%。

## 资源目录

```
assets/
├── scss/
│   └── main.scss       ← resources.Get "scss/main.scss"
└── ts/
    └── main.ts         ← resources.Get "ts/main.ts"
```

Hugo 从主题和站点根目录的 `assets/` 目录读取资源。站点级文件覆盖主题级同名文件——这和模板覆盖逻辑一致。

## 性能

```
# 开发环境编译时间
Transform SCSS:  42ms
Build JS:        38ms
Total:          <100ms
```

不需要 webpack-dev-server，不需要等待 10 秒的热更新。保存文件 → Hugo 检测变化 → 重新编译 → 浏览器自动刷新。不到 200ms。

---

*Hugo 的资源管道是它被低估的功能之一。它可能不会取代 webpack 构建复杂 SPA，但构建一个博客主题绰绰有余。*
