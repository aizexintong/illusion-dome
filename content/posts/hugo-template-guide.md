---
title: "Hugo模板语法：从入门到不想出门"
date: 2026-04-24T14:00:00+08:00
description: "记录Hugo Go Template使用过程中的那些坑和领悟——上下文传递、partial传参、scratch变量和那些令人困惑的语法。"
tags: ["Hugo", "Go Template", "模板", "语法", "踩坑"]
categories: ["技术分享"]
image: "https://t.alcy.cc/ycy?random=21"
---

## 最美的陷阱

Hugo 模板是基于 Go 的 `html/template` 引擎。初看非常简洁——`{{ .Title }}`、`{{ range .Pages }}`——但一旦深入，你会发现一个充满陷阱的世界。

这篇文章记录我在开发幻梦过程中被 Go Template 整蛊的瞬间。

## 上下文（Context）的消失

这是最常见的坑：

```go-html-template
{{ with .Params }}
  <!-- 现在 "." 是 .Params，不再是 Page -->
  {{ .Title }}  <!-- 这访问的是 .Params.Title，不是 Page.Title！-->
{{ end }}
```

`with`、`range` 会改变上下文。解决方案是提前保存引用：

```go-html-template
{{ $page := . }}
{{ range .Pages }}
  {{ $page.Title }}  <!-- 一直指向 Page -->
{{ end }}
```

## Partial 参数传递

```go-html-template
{{ partial "card.html" . }}

<!-- card.html 内部 -->
<h3>{{ .Title }}</h3>  <!-- "." 是被传入的上下文 -->
```

复杂场景需要传递多个参数，用 `dict`：

```go-html-template
{{ partial "card.html" (dict "Page" . "Style" "grid" "Index" $index) }}

<!-- card.html 内部 -->
{{ $page := .Page }}
{{ $style := .Style }}
```

`dict` 创建一个包含键值的 map 作为上下文。这是 Hugo 的 Swiss Army Knife。

## Scratch：共享变量

不同 partial 之间不能直接共享变量。`Scratch` 是 Hugo 的全局便签：

```go-html-template
{{ .Scratch.Set "counter" 0 }}
{{ .Scratch.Add "counter" 1 }}
{{ .Scratch.Get "counter" }}
```

在幻梦中用于跨组件共享状态（如判断文章是否有更新标记）。

## 字符串拼接

Go Template 没有 `+` 运算符用于字符串。要用 `printf`：

```go-html-template
{{ $url := printf "%s/%s" $base $slug }}
```

## 日期格式

Go 的日期格式化用魔法时间 `Mon Jan 2 15:04:05 MST 2006`：

```go-html-template
{{ .Date.Format "2006-01-02" }}           <!-- 2026-04-24 -->
{{ .Date.Format "2006年01月02日" }}        <!-- 2026年04月24日 -->
{{ .Date.Format "January 2, 2006" }}       <!-- April 24, 2026 -->
```

## JSON 输出

`jsonify` 函数将 Go 数据结构转换为 JSON：

```go-html-template
<script>
  window.i18nData = {{ dict "all" $translations | jsonify }};
</script>
```

这在幻梦中用于生成搜索索引和注入 i18n 数据到 JS。

## 最令人困惑的特性

Go Template 没有 `else if`。你要嵌套：

```go-html-template
{{ if eq $a 1 }}
  A
{{ else }}
  {{ if eq $a 2 }}
    B
  {{ else }}
    C
  {{ end }}
{{ end }}
```

---

*Go Template 像一个严格的语文老师。语法对了就很快乐，错了一个标点就报错并且告诉你"undefined"。*
