---
title: "Hero区域设计：第一屏的视觉引力"
date: 2026-04-22T15:00:00+08:00
description: "幻梦首页Hero区域的设计——双列布局、渐变标题、打字机副标题、quote卡片、滚动提示鼠，以及如何在三秒内留住访客。"
tags: ["Hero", "首页", "设计", "视觉", "Typography"]
categories: ["设计理念"]
image: "https://t.alcy.cc/ycy?random=19"
---

## 你有三秒钟

研究显示，网页访客在前三秒决定是否继续浏览。如果你的首页不能在 3 秒内告诉用户"这里值得留下"，他们就走了。

Hero 区域承担了这个使命。

## 双列布局

```
┌──────────────────────┬─────────────────┐
│   徽章                │                 │
│   你好，我是          │   引用 Quote     │
│   ✨ 爱则心痛 ✨       │   "代码是诗..."  │
│   打字机副标题         │                 │
│   描述文字             │   — 作者        │
│   [阅读] [关于]        │                 │
└──────────────────────┴─────────────────┘
```

左侧是核心信息，右侧是大号 Quotes。桌面端双列对称，移动端上下堆叠。

## 渐变标题

名字使用 CSS 渐变文字：

```css
.hero-title {
  background: linear-gradient(135deg, var(--cp4) 0%, var(--ca4) 50%, var(--cg4) 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  font-size: var(--fs5xl);
  font-weight: 700;
}
```

从紫色过渡到金色，与整个主题色彩呼应。这是页面上最抓眼的元素。

## 打字机副标题

使用 Typed.js 轮播 4 行文案，前面详细介绍过。它位于标题下方，给名字增加个性和维度。

## 描述文字

```yaml
description: "欢迎来到我的个人空间，这里记录着我的技术探索、生活感悟和成长历程。"
```

一句话总结博客定位。不超过两行。

## 双 CTA 按钮

两个按钮提供不同的路径：
- **主按钮**（实心渐变）→ 指向文章列表
- **次按钮**（描边透明）→ 指向关于页面

给用户选择，但不给太多选择。两个刚刚好。

## 引用 Quote

右侧的大号引用块，白色文字在深色半透明背景上，使用大号衬线字体风格，增加了页面的书卷气质。

## 滚动提示

底部中央的鼠标动画：

```css
.scroll-hint {
  width: 24px;
  height: 40px;
  border: 2px solid var(--cp4);
  border-radius: 12px;
  position: relative;
}

.scroll-hint::after {
  content: '';
  width: 4px;
  height: 8px;
  background: var(--cp4);
  border-radius: 2px;
  position: absolute;
  top: 8px;
  left: 50%;
  transform: translateX(-50%);
  animation: scrollWheel 2s infinite;
}
```

一个简洁的鼠标图标，滚轮反复向下滚动，暗示"下面还有内容"。

---

*Hero 不是用来炫耀设计技巧的。它是你给新访客的握手。*
