---
title: "技能页设计：能力之美"
date: 2026-05-01T14:00:00+08:00
description: "幻梦的技能展示页面——三区布局（总览+详细+时间线）、进度条动画、项目关联和时间线叙事。"
tags: ["技能", "设计", "进度条", "时间线", "数据驱动"]
categories: ["页面设计"]
image: "https://t.alcy.cc/ycy?random=29"
---

## 技能不是简历

传统简历中的技能列表是一串枯燥的名词和百分比。幻梦试图把技能展示变成一个故事——你学过什么、到什么程度、用来做了什么。

## 三区布局

### 技能总览

```yaml
skills:
  overview:
    categories:
      - icon: "fas fa-code"
        title: "前端开发"
        description: "HTML5、CSS3、JavaScript、React"
        progress: 100
      - icon: "fas fa-server"
        title: "后端开发"
        description: "Node.js、Python、数据库"
        progress: 85
```

四个分类卡片，每个包含图标、标题、描述和动画进度条。进度条在进入视口时从 0% 动画到目标值：

```typescript
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const bar = entry.target as HTMLElement;
      bar.style.width = bar.dataset.level + '%';
      observer.unobserve(bar);
    }
  });
});
```

### 详细技能

每项技能是一张独立卡片：名称 + 熟练度（0-100）+ 进度条 + 描述 + 关联项目列表。关联项目让技能不只是抽象的数字，而是有实际成果的证明。

### 时间线

```yaml
timeline:
  - year: "2022"
    title: "基础学习"
    description: "掌握HTML、CSS、JavaScript基础"
  - year: "2023"
    title: "框架掌握"
    description: "学习Vue.js和React框架"
```

时间线事件左右交替排列，桌面端形成蛇形布局，移动端变为左侧单列。`.timeline-item:nth-child(even)` 右对齐，`nth-child(odd)` 左对齐。

每个事件有一个圆形标记点，用主题色的渐变填充。年份以大号粗体显示在标记点旁。

## 数据驱动

所有技能数据来自 `data/theme.yaml`。修改 YAML 就自动更新页面，完全不需要碰模板。

## 首页技能预览

首页的"技术栈"区域展示前 6 个技能的精简版（仅图标 + 名称 + 进度条），加上"了解更多"按钮链接到完整技能页。

---

*技能展示不是"我会什么"的清单，而是"我做过什么"的叙事。*
