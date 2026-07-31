---
title: "页脚设计：最后一屏的信息汇总"
date: 2026-04-21T10:00:00+08:00
description: "幻梦的页脚设计——四列信息布局、动态版权年份、网站运行计时器、以及如何让页脚成为导航的延伸而非终结。"
tags: ["页脚", "布局", "设计", "JavaScript", "动态"]
categories: ["设计理念"]
image: "https://t.alcy.cc/ycy?random=17"
---

## 页脚不是垃圾桶

很多网站把页脚当作信息堆放地——版权声明、备案号、一堆不相关的链接。这不是页脚，这是杂物间。

幻梦的页脚有清晰的四列布局，每列回答一个问题：

1. **关于** — 这个博客是什么？
2. **快速链接** — 还能去哪？
3. **技术栈** — 博主用什么技术？
4. **关注我** — 怎么联系博主？

## 第一列：关于

```go-html-template
<div class="footer-about">
  <h4>{{ .Site.Title }}</h4>
  <p>{{ .Site.Params.description }}</p>
  <p class="contact-info">
    {{ .Site.Params.email }}
  </p>
</div>
```

博客名称、描述、联系方式。简单直接。

## 第二列：快速链接

使用的导航菜单渲染器，复用 `menu.html` partial，传入 `menuID="main"`。

## 第三列：技术栈

从 `theme.yaml` 的 skills 列表取前 6 个技能，只显示名称和图标，不显示进度条。做一个小型的技术标签云。

## 第四列：社交链接

从 `theme.yaml` 的 social.links 中筛选 `show: true` 的链接，展示图标和平台名，加上个性化座右铭。

## 动态版权年份

```typescript
class FooterManager {
  updateCopyright(): void {
    const year = new Date().getFullYear();
    const el = document.querySelector('.copyright-year');
    if (el) el.textContent = `${year}`;
  }
}
```

无需每年手动更新。

## 网站运行时间

```typescript
updateRunningTime(): void {
  const start = new Date(window.siteConfig.siteTime);
  const now = new Date();
  const diff = now.getTime() - start.getTime();

  const years = Math.floor(diff / (365.25 * 24 * 60 * 60 * 1000));
  const months = Math.floor((diff % (365.25 * 24 * 60 * 60 * 1000)) / (30.44 * 24 * 60 * 60 * 1000));
  const days = Math.floor((diff % (30.44 * 24 * 60 * 60 * 1000)) / (24 * 60 * 60 * 1000));
  const hours = Math.floor((diff % (24 * 60 * 60 * 1000)) / (60 * 60 * 1000));
  const minutes = Math.floor((diff % (60 * 60 * 1000)) / (60 * 1000));

  // 显示: X年 Y月 Z天 H小时 M分钟
}
```

每个时间单位用不同颜色显示，让页脚底部多了一抹彩色。

## 渐变顶边

```css
.footer {
  border-top: 2px solid;
  border-image: linear-gradient(135deg, var(--cp4), var(--ca4)) 1;
}
```

一条从紫到金的渐变线将页脚与正文区域分隔，视觉上干净利落。

---

*页脚是用户滚动到尽头时看到的最后内容。让它成为句号，不是省略号。*
