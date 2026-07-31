---
title: "工具页设计：一个开发者的数字工具箱"
date: 2026-05-03T16:00:00+08:00
description: "幻梦的工具推荐页面——分类展示、图标和链接、以及如何将日常使用的数字工具整理成一个有美感的推荐列表。"
tags: ["工具", "设计", "推荐", "开发效率", "页面"]
categories: ["页面设计"]
image: "https://t.alcy.cc/ycy?random=31"
---

## 每个开发者都有一组偏爱的工具

VS Code 还是 JetBrains？Figma 还是 Sketch？Notion 还是 Obsidian？这些工具选择定义了开发者的工作流程和审美偏好。

幻梦的工具页把这些选择整理成一个有美感的推荐列表。

## 分类布局

```yaml
tools:
  categories:
    - name: "开发工具"
      icon: "fas fa-code"
      description: "编程开发相关的工具软件"
      tools:
        - name: "Visual Studio Code"
          url: "https://code.visualstudio.com"
          description: "强大的代码编辑器"
          icon: "https://cdn.jsdelivr.net/gh/simple-icons/simple-icons/icons/visualstudiocode.svg"
```

每个分类是一个独立的区块：
- 分类标题 + 图标 + 描述
- 工具卡片网格（auto-fill，最小宽度 200px）
- 每张卡片：工具图标（支持 URL 图片或 FontAwesome 图标）+ 名称 + 描述 + 箭头链接

## 两种图标模式

```go-html-template
{{ if hasPrefix .icon "http" }}
  <img src="{{ .icon }}" alt="{{ .name }}" />
{{ else }}
  <i class="{{ .icon }}"></i>
{{ end }}
```

如果图标是 URL → 渲染为 `<img>`。如果是 FontAwesome class → 渲染为 `<i>`。两种模式统一处理。

## 外链处理

所有工具链接都添加 `target="_blank"` 和 `rel="noopener noreferrer"`：

```html
<a href="https://code.visualstudio.com" target="_blank" rel="noopener noreferrer">
  访问 <i class="fas fa-arrow-up-right-from-square"></i>
</a>
```

箭头图标暗示这是外部链接，保持用户的空间感知。

## 为什么要做工具页？

工具页的价值不只是"告诉别人我用什么"。它是：
1. **个人知识管理的延伸**——整理和回顾自己依赖的工具
2. **对读者的参考价值**——如果你喜欢这个博客，可能也会对博主的工具选择感兴趣
3. **页面多样性**——不是所有页面都在讲技术，偶尔来一页轻松的工具推荐

---

*工具页是一封给读者的"这是我用什么"的信。它让博客多了一层人格化的维度。*
