---
title: "标签与分类：Hugo分类法的优雅呈现"
date: 2026-04-08T14:00:00+08:00
description: "幻梦主题的标签和分类系统——格子式展示、客户端分页、文章数量统计，让Hugo的分类法变得更实用、更美观。"
tags: ["标签", "分类", "Hugo", "分类法", "分页"]
categories: ["功能特性"]
image: "https://t.alcy.cc/ycy?random=6"
---

## 为什么分类法很重要？

Hugo 原生支持 tags 和 categories 两种分类法。但默认的展示方式——一个纯文本的标签列表——既不好看也不实用。

标签应该是探索内容的入口。幻梦重新设计了标签和分类的展示方式。

## 格子式标签云

```go-html-template
<div class="tags-grid">
  {{ range .Data.Terms.Alphabetical }}
    <a href="{{ .Page.Permalink }}" class="tag-card">
      <span class="tag-name">{{ .Page.Title }}</span>
      <span class="tag-count">{{ .Count }}</span>
    </a>
  {{ end }}
</div>
```

每个标签渲染为一个卡片，包含标签名和文章数量。大片留白让视觉呼吸，标签数量用柔和的灰色提示。

Hover 时卡片左边框出现渐变色条，从紫色过渡到粉色，呼应主题色彩系统。

## 客户端分页

标签太多时（50+），一页显示全部标签会导致页面过长。幻梦引入了客户端分页：

```typescript
class TagsPagination {
  private page = 1;
  private perPage = 25;  // 从 theme.yaml 读取

  paginate(): void {
    const start = (this.page - 1) * this.perPage;
    const end = start + this.perPage;
    const visible = this.tags.slice(start, end);

    this.renderTags(visible);
    this.updatePaginationButtons();
  }
}
```

分页状态通过 URL query 参数同步，分享带分页的链接不会丢失状态：

```
/tags/?page=3
```

## 标签页内的文章列表

点击某个标签进入标签页，显示该标签下的所有文章，和文章列表页一样的卡片布局，统一体验。

## 分类页同理

分类（categories）页面使用相同的设计模式，只是数据来源不同。一个设计，两个入口，减少维护成本。

---

*标签不是文章的附属品。它是通往你知识图谱的路标。*
