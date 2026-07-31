---
title: "数据驱动设计：YAML管理全站的优雅"
date: 2026-05-05T13:00:00+08:00
description: "幻梦的数据驱动架构——为什么把页面内容放到data/theme.yaml、Hugo的数据文件机制、以及如何让非开发者也能管理博客。"
tags: ["数据驱动", "YAML", "Hugo", "架构", "配置"]
categories: ["技术分享"]
image: "https://t.alcy.cc/ycy?random=34"
---

## 把内容和展示分离

传统博客的管理方式：改首页文案 → 打开 `index.html` → 在 Go Template 中寻找文字 → 修改 → 构建 → 检查。

如果不小心删了一个 `</div>`，整个页面崩溃。

幻梦的做法：所有页面内容放到 `data/theme.yaml`，模板只负责展示逻辑。

## Hugo 的数据文件机制

Hugo 会自动加载 `data/` 目录下的 YAML/JSON/TOML 文件，通过 `.Site.Data` 访问：

```
data/
└── theme.yaml     →  .Site.Data.theme
```

站点目录的 `data/` 会自动覆盖主题目录的同名文件。这正是幻梦的定制策略：

```
themes/illusion/data/theme.yaml   ← 默认值
data/theme.yaml                    ← 你的定制（覆盖默认）
```

## 模板中的访问

```go-html-template
<h1>{{ hugo.Data.theme.home.hero.title }}</h1>
<p>{{ hugo.Data.theme.home.hero.description }}</p>

{{ range hugo.Data.theme.home.hero.subtitles }}
  <span>{{ . }}</span>
{{ end }}
```

模板永远不包含具体的文字内容，只引用数据路径。

## 为什么放在一个文件里？

最初幻梦有 9 个独立的 YAML 文件（`home.yaml`、`about.yaml`、`skills.yaml`……）。但管理 9 个文件比重写模板好不到哪里去。

合并为一个 `theme.yaml` 后，一个文件就能看到全站配置。Ctrl+F 就能找到任何需要修改的内容。

## 对非开发者的友好

某一天，你想把博客交给一个不懂代码的朋友维护。你需要教他的只有一句话：

> "打开 `data/theme.yaml`，改里面的文字就行。"

不需要解释 Go Template、不需要碰 HTML。改 YAML → Hugo 重新构建 → 网站更新。

## 未来可能的方向

YAML 驱动的内容管理有其极限。当博客有 100+ 篇文章时，前端编辑的需求会出现。但在这个规模之前，一个 YAML 文件比任何 CMS 都更简单、更可靠。

---

*最好的内容管理系统，有时候就是一个文本文件。*
