---
title: "评论系统：连接作者与读者的桥梁"
date: 2026-05-07T10:00:00+08:00
description: "幻梦的评论系统设计——支持Disqus、Utterances和Giscus三种评论方案，以及为什么个人博客仍然需要评论区。"
tags: ["评论", "Disqus", "Utterances", "Giscus", "社区"]
categories: ["功能特性"]
image: "https://t.alcy.cc/ycy?random=36"
---

## 博客需要评论吗？

在社交媒体时代，大多数讨论发生在 Twitter、Reddit 和各个技术社区。个人博客的评论区似乎有些过时。

但评论区有一个无可替代的价值：**它在你的博客上。** 评论是你和读者之间不经过任何平台中介的直接对话。它们存在于你的空间里，不会因为某个社交平台倒闭而消失。

## 三种方案

幻梦支持三种评论系统，通过 `hugo.toml` 切换：

```toml
[params]
  enableComments = true

  [params.comments]
    provider = "giscus"    # disqus | utterances | giscus
```

### Disqus

最老牌的第三方评论系统。优点：功能完善、支持社交登录。缺点：有广告、加载较慢、隐私争议。

```go-html-template
{{ if eq $provider "disqus" }}
  {{ template "_internal/disqus.html" . }}
{{ end }}
```

### Utterances

基于 GitHub Issues 的轻量评论。优点：无广告、开源、数据在 GitHub 上。缺点：评论者需要 GitHub 账号。

```go-html-template
<script src="https://utteranc.es/client.js"
  repo="{{ .Site.Params.comments.utterances.repo }}"
  issue-term="pathname"
  theme="preferred-color-scheme"
  crossorigin="anonymous"
  async>
</script>
```

### Giscus

Utterances 的现代替代品，基于 GitHub Discussions。配置类似，但支持更多自定义选项（包括主题跟随）。

## 为什么默认关了？

幻梦主题默认 `enableComments = false`。原因很简单：大多数新博客刚开始没有读者。在空白评论区展示"0 comments"不如不展示。

当博客有了稳定的读者群后再开启评论区——这是从演讲台到圆桌的转变。

## 评论区的设计

评论区位于文章正文之后、文章导航之前。使用半透明卡片包裹，与页面风格统一。评论表单和评论列表都适配了深色/浅色模式（如果评论系统支持主题切换）。

---

*评论区是博客的公共广场。让它存在，但不必让它成为负担。*
