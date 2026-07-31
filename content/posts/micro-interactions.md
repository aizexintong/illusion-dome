---
title: "微交互：那些让你微笑的细节"
date: 2026-04-29T13:00:00+08:00
description: "幻梦的微交互设计——涟漪按钮、3D卡片、滚动进度条、点击反馈，以及为什么好的交互是让用户不知道它在那儿。"
tags: ["微交互", "UX", "动画", "CSS", "交互设计"]
categories: ["设计理念"]
image: "https://t.alcy.cc/ycy?random=27"
---

## 微交互不是装饰

微交互（Micro-interactions）是用户完成一个小动作时得到的反馈。点击一个按钮，它微微凹陷。滚动页面，顶部出现进度条。鼠标悬停，卡片倾斜。

这些不是花里胡哨。它们回答一个根本问题：**系统收到我的操作了吗？**

## 涟漪按钮

点击按钮时，以点击位置为中心扩散圆形波纹：

```css
.btn::after {
  content: '';
  position: absolute;
  top: var(--ripple-y);
  left: var(--ripple-x);
  width: 0;
  height: 0;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.4);
  transform: translate(-50%, -50%);
  animation: ripple 0.6s ease-out;
}

@keyframes ripple {
  to {
    width: 300px;
    height: 300px;
    opacity: 0;
  }
}
```

JavaScript 负责获取点击坐标并设置 CSS 变量：

```typescript
button.addEventListener('click', (e) => {
  const rect = button.getBoundingClientRect();
  button.style.setProperty('--ripple-x', `${e.clientX - rect.left}px`);
  button.style.setProperty('--ripple-y', `${e.clientY - rect.top}px`);
});
```

涟漪从点击位置开始扩散——模拟了物理世界的触摸反馈。

## 3D 卡片倾斜

```typescript
apply3DEffect(card: HTMLElement, e: MouseEvent): void {
  const rect = card.getBoundingClientRect();
  const x = (e.clientX - rect.left) / rect.width - 0.5;
  const y = (e.clientY - rect.top) / rect.height - 0.5;

  requestAnimationFrame(() => {
    card.style.transform = `
      perspective(1000px)
      rotateY(${x * 5}deg)
      rotateX(${-y * 5}deg)
    `;
  });
}
```

鼠标靠近卡片右侧时卡片向右旋转，靠近上侧时向上翘起。模拟一张真实卡片在手中的倾斜。使用 `requestAnimationFrame` 确保流畅的 60fps。

## 滚动进度条

```css
.scroll-progress {
  position: fixed;
  top: 0;
  left: 0;
  height: 2px;
  background: linear-gradient(90deg, var(--cp4), var(--ca4));
  transform-origin: left;
  transform: scaleX(var(--scroll-percent, 0));
  transition: transform 0.1s linear;
  z-index: 999;
}
```

JavaScript 监听滚动，更新 `--scroll-percent` CSS 变量。一个渐变色条跟随阅读进度延伸，既是反馈也是进度指示。

## 点击涟漪之外的反馈

- **复制按钮**：点击后文字变成"已复制！"，2 秒后复原
- **主题切换按钮**：图标在太阳/月亮/自动之间切换，带旋转过渡
- **搜索按钮**：点击后模态框从中央放大出现
- **标签 hover**：左边框出现渐变色条

## 不做过度交互

微交互的陷阱是过度设计。幻梦的原则：

- 每个交互必须有明确的反馈意义
- 动画不超过 400ms
- 尊重 `prefers-reduced-motion`
- 不打断用户的阅读流

---

*最好的微交互是用户不会注意到的——但一旦去掉，他们会觉得"哪里不对"。*
