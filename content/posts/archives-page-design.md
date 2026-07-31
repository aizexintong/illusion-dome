---
title: "时间线归档：年月的叙事结构"
date: 2026-04-07T11:00:00+08:00
description: "幻梦的归档页面设计——按年月分组、侧边栏年份导航、月份网格、文章统计，将时间线转化为可浏览的叙事结构。"
tags: ["归档", "时间线", "设计", "Hugo"]
categories: ["功能特性"]
image: "https://t.alcy.cc/ycy?random=5"
---

## 归档不是列表

将文章按日期排序输出是最简单的归档方式，也是大多数博客的做法。但这不是好的设计。

好的归档应该让读者感知到时间的厚度——哪些月份高产、哪些主题在哪个时期集中出现、作者的兴趣如何随时间演变。

幻梦的归档页面做了三件事：分组、导航、统计。

## 按年月分组

```go-html-template
{{ range .Pages.GroupByDate "2006" }}
  <section class="archive-year" id="year-{{ .Key }}">
    <h2>{{ .Key }}</h2>
    {{ range .Pages.GroupByDate "January" }}
      <h3>{{ .Key }}</h3>
      {{ range .Pages }}
        <article>{{ .Title }}</article>
      {{ end }}
    {{ end }}
  </section>
{{ end }}
```

`GroupByDate "2006"` 按年分组，内层 `GroupByDate "January"` 按月分组。Hugo 的日期格式化字符串遵循 Go 的魔法时间 `Mon Jan 2 15:04:05 MST 2006`。

## 侧边栏导航

右侧侧边栏包含两部分：

**年份导航**：列出所有有文章的年份，点击跳转到对应区域。带有"上一页/下一页"按钮，方便键盘导航。

**月份网格**：当前选中年份的 12 个月网格，有文章的月份高亮显示，点击跳转到对应月份。

```typescript
class ArchivesNavigator {
  // 监听滚动，自动高亮当前所在年份
  observeYearSections(): void {
    const observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          this.highlightYear(entry.target.id);
        }
      });
    }, { threshold: 0.3 });
  }
}
```

## 文章统计

侧边栏底部显示全局统计：总文章数、涉及月份数、最早文章日期、最新文章日期。给读者一个整体的时间感知。

## 视觉层次

年份标题用大号渐变文字，月份标题中等大小，文章标题正常大小加上日期。三层信息通过字号、颜色和间距区分，扫一眼就能找到目标。

---

*时间线不只是日期的排列。它是你写作生活的年轮。*
