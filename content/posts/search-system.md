---
title: "客户端搜索：一次JSON请求搞定全站搜索"
date: 2026-04-02T08:00:00+08:00
description: "幻梦主题的本地搜索引擎——Hugo生成JSON索引，TypeScript实现前端全文搜索，支持关键词高亮和Ctrl+K快捷键。"
tags: ["搜索", "JSON", "JavaScript", "性能"]
categories: ["功能特性"]
image: "https://t.alcy.cc/ycy?random=2"
---

## 为什么不需要后端搜索？

很多博客使用 Algolia 或自建搜索 API，但对于个人博客来说，一个 JSON 文件就够了。

幻梦的搜索方案：Hugo 编译时生成一个 `index.json`，包含全站文章索引；用户搜索时在前端直接匹配，无需任何后端服务。

## 生成索引

`layouts/_default/index.json` 负责生成搜索索引：

```go-html-template
{{- range where .Site.RegularPages "Type" "posts" -}}
{
  "title": {{ .Title | jsonify }},
  "permalink": {{ .Permalink | jsonify }},
  "date": {{ .Date.Format "2006-01-02" | jsonify }},
  "description": {{ .Description | jsonify }},
  "content": {{ .Plain | truncate 500 | jsonify }},
  "tags": {{ .Params.tags | jsonify }},
  "categories": {{ .Params.categories | jsonify }}
}
{{- end -}}
```

每篇文章提取标题、链接、日期、描述、前 500 字正文和标签分类。一个 50 篇文章的博客，索引通常不超过 200KB。

## 搜索算法

```typescript
class SearchEngine {
  private index: SearchEntry[] = [];

  search(query: string): SearchResult[] {
    const q = query.toLowerCase().trim();
    if (q.length < 1) return [];

    return this.index
      .filter(entry =>
        entry.title.toLowerCase().includes(q) ||
        entry.content.toLowerCase().includes(q) ||
        entry.tags.some(t => t.toLowerCase().includes(q)) ||
        (entry.description && entry.description.toLowerCase().includes(q))
      )
      .map(entry => ({
        ...entry,
        score: this.calculateScore(entry, q),
        excerpt: this.generateExcerpt(entry.content, q)
      }))
      .sort((a, b) => b.score - a.score)
      .slice(0, 10);
  }
}
```

匹配优先级：标题匹配 > 标签匹配 > 正文匹配。搜索结果按相关度排序，只返回前 10 条。

## 关键词高亮

搜索结果中的关键词用 `<mark>` 标签高亮：

```typescript
highlight(text: string, query: string): string {
  const regex = new RegExp(`(${this.escapeRegex(query)})`, 'gi');
  return text.replace(regex, '<mark>$1</mark>');
}
```

## 快捷键支持

搜索模态框支持 `Ctrl+K` / `Cmd+K` 全局快捷键：

```typescript
document.addEventListener('keydown', (e) => {
  if ((e.ctrlKey || e.metaKey) && e.key === 'k') {
    e.preventDefault();
    this.open();
  }
});
```

## 上下文摘录

搜索结果展示包含关键词的上下文片段（前 30 字符 + 关键词 + 后 70 字符），让用户判断结果是否相关。

---

*一个 JSON 文件，几行 TypeScript，零后端依赖。个人博客的搜索就该这么简单。*
