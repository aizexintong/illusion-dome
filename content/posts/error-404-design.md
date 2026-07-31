---
title: "404页面：错误也可以是风景"
date: 2026-04-19T13:00:00+08:00
description: "幻梦的404错误页面设计——可爱的小幽灵、暖心的文案、快捷导航按钮，让一个技术错误变得不那么令人沮丧。"
tags: ["404", "错误页面", "设计", "用户体验"]
categories: ["设计理念"]
image: "https://t.alcy.cc/ycy?random=16"
---

## 404 是用户离开的高危时刻

用户看到一个死链接，第一反应是失望。如果 404 页面只有"Page Not Found"和浏览器默认的白底黑字，用户大概率关掉标签页。

好的 404 页面应该做三件事：**安抚情绪、提供出口、保持品牌一致性。**

## 数据驱动

```yaml
error404:
  page:
    title: "404"
    description: "页面不存在或已被移除"
    message: "抱歉，你访问的页面不存在。可能已被删除或链接有误。"
    icon: "fas fa-ghost"
  buttons:
    home:
      text: "返回首页"
      icon: "fas fa-home"
      url: "/"
    posts:
      text: "浏览文章"
      icon: "fas fa-file-alt"
      url: "/posts/"
```

全部在 `theme.yaml` 中配置，随时调整文案。

## 视觉设计

页面中央是一个动态的小幽灵图标，使用 CSS 浮动动画：

```css
.error-icon {
  font-size: 6rem;
  color: var(--cpl);
  animation: float 3s ease-in-out infinite;
}

@keyframes float {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
}
```

"404" 大字用渐变文字，从主题色过渡到金色，与首页标题呼应。

## 两个行动按钮

不是只有一个"返回首页"。给了两个选择：

1. **返回首页**（主按钮，实心渐变）——大概率的选择
2. **浏览文章**（次按钮，描边透明）——提供了另一个探索入口

多一个选择，可能多留一个用户。

## 背景粒子

和首页一样，404 页也有 Canvas 粒子背景（只是粒子密度降低）。即使在这个"错误"的页面，用户仍然在读你的博客，所以体验应该是一致的。

## 搜索引擎友好

```html
<meta name="robots" content="noindex" />
```

404 页不应该出现在搜索结果中。但用户可以停留在这里继续探索。

---

*错误不可避免，但用户对错误的感受是可以设计的。*
