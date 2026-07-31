---
title: "侧边栏设计：文章页的辅助空间"
date: 2026-05-06T15:00:00+08:00
description: "幻梦的侧边栏——文章目录的滚动高亮、发布信息、标签云、日历组件、以及侧边栏与正文之间如何建立关联。"
tags: ["侧边栏", "TOC", "设计", "UX", "导航"]
categories: ["页面设计"]
image: "https://t.alcy.cc/ycy?random=35"
---

## 侧边栏的三种角色

文章页侧边栏在不同的上下文中扮演不同的角色：

1. **文章页侧边栏**：目录、发布信息、标签
2. **列表页侧边栏**：日历、最新文章、标签云
3. **归档页侧边栏**：年份导航、月份网格、统计

## 文章目录（TOC）

Hugo 自动从 Markdown 标题生成目录：

```go-html-template
{{ if and .Params.toc .TableOfContents }}
  <div class="toc-widget">
    <h3>{{ i18n "article.toc" }}</h3>
    {{ .TableOfContents }}
  </div>
{{ end }}
```

但在大屏幕上，一个静态的目录列表意义有限。幻梦让 TOC 与阅读进度联动：

```typescript
class UtilsManager {
  observeHeadings(): void {
    const observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          // 高亮当前可见标题对应的 Toc 链接
          this.highlightTocLink(entry.target.id);
        }
      });
    }, { rootMargin: '-80px 0px -60% 0px' });

    document.querySelectorAll('h2[id], h3[id]').forEach(h => observer.observe(h));
  }
}
```

当用户滚动到某个二级标题时，侧边栏中对应的目录项自动高亮。TOC 从"地图"变成了"GPS"。

## 发布信息

文章元数据卡片：

- 发布时间（相对时间 + 绝对日期）
- 最后修改时间（如果有，标记"已更新"）
- 字数统计
- 阅读时间

这些信息在移动端不显示在侧边栏，而是移到正文标题下方。

## 标签云

文章的所有标签以胶囊形状排列。点击任一标签跳转到标签页，列出该标签下的所有文章。

## 日历组件

列表页和归档页的侧边栏包含一个 JavaScript 渲染的日历：

```typescript
class CalendarWidget {
  render(year: number, month: number): void {
    const firstDay = new Date(year, month, 1).getDay();
    const daysInMonth = new Date(year, month + 1, 0).getDate();

    // 渲染月名（中文）、星期头、日期网格
    // 当天高亮
    // 有文章的日期用圆点标记
  }
}
```

一个简洁的日历，当前月份高亮，当天用主题色标注。

## 响应式行为

```css
/* 桌面端：侧边栏在右侧，position: sticky */
@media (min-width: 768px) {
  .sidebar {
    position: sticky;
    top: calc(var(--header-height) + var(--s6));
    width: var(--sidebar-width);
  }
}

/* 移动端：侧边栏在正文下方，正常流 */
@media (max-width: 767px) {
  .sidebar {
    position: static;
    width: 100%;
    margin-top: var(--s8);
  }
}
```

---

*好的侧边栏不是内容的堆砌。它是一个恰到好处的辅助——需要时在那里，不需要时不打扰。*
