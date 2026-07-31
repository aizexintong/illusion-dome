---
title: "Hugo模板语法：从入门到写出一个主题"
date: 2026-05-04T10:00:00+08:00
description: "Hugo Go Template语法核心概念——变量、条件、循环、partial、block、以及如何用它们构建一个完整的数据驱动主题。"
tags: ["Hugo", "Go Template", "模板", "教程", "语法"]
categories: ["技术分享"]
image: "https://t.alcy.cc/ycy?random=32"
---

## Go Template 的第一印象

如果你来自 Jinja2（Python）、ERB（Ruby）或 Blade（PHP），Go Template 的语法会让你皱眉。它用 `{{ }}` 而非 `{% %}`，用 `.` 代表上下文，用 `range` 而非 `for`。

但一旦适应，你会发现它的克制是一种美德。

## 基础语法

### 变量输出

```go-html-template
{{ .Title }}         {{/* 当前页面的标题 */}}
{{ .Site.Title }}    {{/* 站点标题 */}}
{{ .Date.Format "2006-01-02" }}  {{/* 格式化日期 */}}
```

`.` 是当前上下文，通过 `.FieldName` 访问字段。

### 条件

```go-html-template
{{ if .Params.image }}
  <img src="{{ .Params.image }}" />
{{ else }}
  <span>无封面图</span>
{{ end }}
```

### 循环

```go-html-template
{{ range .Pages }}
  <a href="{{ .Permalink }}">{{ .Title }}</a>
{{ end }}
```

`range` 循环中 `.` 变为每个迭代元素的上下文。

### 变量赋值

```go-html-template
{{ $myVar := "hello" }}
{{ $count := len .Pages }}
```

使用 `:=` 赋值，`$` 前缀标记变量。

## Partial：模板的函数

```go-html-template
{{ partial "header.html" . }}
{{ partial "card.html" (dict "Page" . "Style" "grid") }}
```

`partial` 是 Go Template 的"组件"机制。第二个参数传递上下文。使用 `dict` 可以传递多个命名参数。

## Block：模板的继承

```go-html-template
{{/* baseof.html */}}
<html>
<body>
  {{ block "main" . }}{{ end }}
</body>
</html>

{{/* single.html */}}
{{ define "main" }}
  <article>{{ .Content }}</article>
{{ end }}
```

`block "main"` 是插槽，子模板用 `define "main"` 填充。这是 Hugo 模板继承的基础。

## 数据访问

### Front Matter

```go-html-template
{{ .Params.tags }}       {{/* 自定义参数 */}}
{{ .Date }}              {{/* 内置日期 */}}
{{ .WordCount }}         {{/* 内置字数 */}}
{{ .ReadingTime }}       {{/* 分钟 */}}
```

### 站点数据

```go-html-template
{{ .Site.Data.theme.home.hero.title }}    {{/* data/theme.yaml */}}
{{ .Site.Params.author }}                 {{/* hugo.toml [params] */}}
{{ i18n "post.readingTime" .ReadingTime }} {{/* i18n 翻译 */}}
```

### 分类法

```go-html-template
{{ range .Site.Taxonomies.tags }}
  <a href="{{ .Page.Permalink }}">{{ .Page.Title }} ({{ .Count }})</a>
{{ end }}
```

## 常用函数

```go-html-template
{{ truncate 160 .Summary }}           {{/* 截断文本 */}}
{{ .Title | lower }}                  {{/* 管道：转小写 */}}
{{ .Title | urlize }}                 {{/* URL 友好化 */}}
{{ range first 6 .Pages }}            {{/* 取前6个 */}}
{{ range after 3 .Pages }}            {{/* 跳过前3个 */}}
{{ .RelPermalink }}                   {{/* 相对URL */}}
{{ .Permalink }}                      {{/* 绝对URL */}}
```

## 实战：渲染文章卡片

```go-html-template
{{ range .Paginator.Pages }}
  <article class="post-card" data-aos="fade-up">
    {{ if .Params.image }}
      <img src="{{ .Params.image }}" alt="{{ .Title }}" loading="lazy" />
    {{ end }}
    <div class="card-content">
      <h2><a href="{{ .Permalink }}">{{ .Title }}</a></h2>
      <div class="meta">
        <time>{{ .Date.Format "2006-01-02" }}</time>
        <span>{{ .ReadingTime }} 分钟</span>
      </div>
      <p>{{ .Summary | truncate 160 }}</p>
      <div class="tags">
        {{ range .Params.tags }}
          <a href="/tags/{{ . | urlize }}">{{ . }}</a>
        {{ end }}
      </div>
    </div>
  </article>
{{ end }}
```

这就是幻梦中 `article-card.html` partial 的核心逻辑。看起来简单，但已经覆盖了 80% 的博客展示需求。

---

*Go Template 的语法不华丽——但一年后回来看，你会感谢当初选择了克制。*
