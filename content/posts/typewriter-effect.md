---
title: "打字机效果：一行文字的诗意"
date: 2026-04-14T10:00:00+08:00
description: "幻梦首页的打字机效果——Typed.js的配置、多行文案轮播、与Canvas粒子系统的配合，以及如何用一句话抓住读者。"
tags: ["打字机", "Typed.js", "动画", "首页", "交互"]
categories: ["功能特性"]
image: "https://t.alcy.cc/ycy?random=12"
---

## 一行能打动人心的文字

首页 Hero 区域是用户对你的第一印象。标题够大，描述够清晰——但还缺一样东西：**动态的个性表达。**

幻梦用 Typed.js 在首页副标题处创建打字机效果，轮播多行文案。

## 配置方式

```yaml
# data/theme.yaml
home:
  hero:
    subtitles:
      - "技术开发者"
      - "代码艺术家"
      - "终身学习者"
      - "梦想追逐者"
```

四行文案循环打字，每行打完停顿 2 秒后删除，再打下一行。

## 初始化

```typescript
class AnimationManager {
  initTypewriter(): void {
    const el = document.querySelector('[data-typed]');
    if (!el) return;

    const strings = JSON.parse(el.getAttribute('data-typed') || '[]');

    new Typed(el, {
      strings,
      typeSpeed: 60,        // 打字速度 (ms/字符)
      backSpeed: 40,        // 删除速度
      backDelay: 2000,      // 打完后的停顿
      loop: true,           // 循环播放
      showCursor: true,     // 显示光标
      cursorChar: '|',      // 光标字符
    });
  }
}
```

Hugo 模板将 YAML 数据注入为 `data-typed` 属性，TypeScript 读取并初始化。

## 为什么是打字机效果？

打字机效果在 2026 年已经算不上新鲜。但它仍然有效，原因有二：

1. **暗示创作过程**：打字机暗示这些文字是"正在被写下的"，给人一种实时、真诚的感觉
2. **聚焦注意力**：动态文字天然吸引视线，让用户注意到你想传达的核心信息

## 光标设计

闪烁的光标 `|` 用 CSS 动画实现：

```css
.typed-cursor {
  opacity: 1;
  animation: blink 1s infinite;
  color: var(--cp4);
}

@keyframes blink {
  0%, 100% { opacity: 1; }
  50% { opacity: 0; }
}
```

光标颜色使用主题色，与标题的渐变文字呼应。

## 与粒子的配合

打字机效果运行在 Canvas 粒子背景之上。浅色模式下糖果粒子飘落，光标闪烁——组合出一种动态但不过于热闹的首页氛围。

---

*好的首页不是堆砌信息。一句打动人的话，比十段描述更有力量。*
