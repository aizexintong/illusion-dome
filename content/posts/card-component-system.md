---
title: "卡片系统：幻梦的组件基石"
date: 2026-04-17T09:00:00+08:00
description: "幻梦主题的卡片组件系统——从文章卡片到技能卡片再到友链卡片，统一的玻璃态设计语言和可切换的布局模式。"
tags: ["组件", "卡片", "CSS", "设计系统", "布局"]
categories: ["设计理念"]
image: "https://t.alcy.cc/ycy?random=14"
---

## 卡片是博客的积木

浏览任何现代博客，你会发现页面基本上是由"卡片"组成的：文章预览是卡片、技能展示是卡片、友链是卡片、侧边栏是卡片。

幻梦认识到这一点，建立了一个统一的卡片基类，所有具体卡片变体都继承它的视觉语言。

## 卡片基类

```css
.card {
  background: var(--cbg2);
  backdrop-filter: blur(16px);
  border: 1px solid var(--cbd);
  border-radius: var(--br2);
  padding: var(--s6);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.card:hover {
  transform: translateY(-4px);
  box-shadow: var(--sh2);
}
```

三个要素：玻璃态外观、悬浮提升、统一间距。所有卡片变体都从这里延伸。

## 文章卡片

```go-html-template
{{ partial "_components/article-card.html" (dict
  "Page" .
  "Style" "grid"
  "ExcerptLength" 160
  "ShowImage" true
  "Index" $index
) }}
```

`article-card.html` 支持两种模式：
- **grid**：顶部封面图 + 底部文字，适合文章列表
- **list**：图片和文字左右排列，适合分类页

封面图区域覆盖了分类标签（半透明渐变背景 + 白色文字），hover 时封面图微微缩放。

## 技能卡片

技能卡片更简洁：图标 + 名称 + 进度条 + 描述 + 项目列表。进度条的宽度在进入视口时通过 IntersectionObserver 从 0% 动画到目标值。

## 友链卡片

友链卡片包含：头像图片 + 站点名 + 描述 + 分类标签 + 访问链接。hover 时头像微微放大，链接变成渐变文字。

## 统一的 hover 行为

所有卡片 hover 时：
1. 向上平移 4-8px
2. 阴影加深
3. 顶部渐变线条出现（`::before` 伪元素）
4. 过渡 300ms ease-out

一致性让用户在任何页面都知道"这是一个可以交互的卡片"。

## 3D 悬停效果

对于启用了 3D 效果的环境，卡片额外支持鼠标位置驱动的透视变换：

```typescript
class InteractionManager {
  apply3DEffect(card: HTMLElement, e: MouseEvent): void {
    const rect = card.getBoundingClientRect();
    const x = (e.clientX - rect.left) / rect.width - 0.5;
    const y = (e.clientY - rect.top) / rect.height - 0.5;

    card.style.transform = `
      perspective(1000px)
      rotateY(${x * 5}deg)
      rotateX(${-y * 5}deg)
      translateY(-4px)
    `;
  }
}
```

鼠标靠近卡片边缘时卡片微微倾斜，模拟物理卡片的光线反射。`requestAnimationFrame` 节流确保 60fps。

---

*一个博客就是一堆卡片的有序排列。把卡片做好，博客就做好了一半。*
