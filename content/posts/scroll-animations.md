---
title: "滚动动画与AOS：让页面活起来"
date: 2026-04-13T15:00:00+08:00
description: "幻梦使用AOS.js实现滚动动画——从初始化配置到自定义fallback，以及如何在不影响性能的前提下为页面添加层次感。"
tags: ["动画", "AOS", "滚动", "性能", "CSS"]
categories: ["功能特性"]
image: "https://t.alcy.cc/ycy?random=11"
---

## 动画是有用的

有人认为网页动画是花瓶。我不这么看。

好的动画提供**空间关系**——元素从哪里来、到哪里去、它们之间如何关联。当卡片从下方淡入时，你潜意识里理解了页面的信息层次。这不是装饰，是引导。

## AOS 初始化

```typescript
class AnimationManager {
  initAOS(): void {
    AOS.init({
      duration: 400,           // 动画时长
      easing: 'ease-out-cubic', // 缓动曲线
      once: true,              // 只触发一次
      offset: 80,              // 触发偏移量
    });
  }
}
```

`once: true` 避免了重复触发（尤其在页面反复滚动时）。`offset: 80` 意味着元素进入视口前 80px 就开始动画，确保到达可见区域时动画已经完成。

## 使用方式

在 HTML 中添加 `data-aos` 属性：

```html
<div data-aos="fade-up" data-aos-delay="100">
  <!-- 卡片内容 -->
</div>
```

Hugo 模板中：

```go-html-template
<div data-aos="fade-up" data-aos-delay="{{ mul .index 100 }}">
  {{ .Title }}
</div>
```

每一张卡片比前一张延迟 100ms，形成波浪式的出场效果。

## Fallback 机制

AOS 从 CDN 加载，如果加载失败怎么办？

```typescript
// CDN 加载失败时，使用 IntersectionObserver 做简易动画
function fallbackScrollAnimation(): void {
  if (typeof AOS !== 'undefined') return;  // AOS 已加载

  const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        entry.target.classList.add('aos-animate');
        observer.unobserve(entry.target);
      }
    });
  });

  document.querySelectorAll('[data-aos]').forEach(el => observer.observe(el));
}
```

没有动画库也能有简约的淡入效果。

## 动画层次

不是所有元素都应该动画。幻梦的分层策略：

- **Hero 区域**：标题从左、描述从下、按钮延迟出现
- **卡片网格**：波浪式逐个淡入
- **侧边栏部件**：从右滑入
- **正文内容**：没有动画——阅读时动画是干扰

## 尊重用户偏好

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

开启了 `prefers-reduced-motion` 的用户（通常是前庭功能障碍者）不会看到任何动画。

---

*好的动画是页面编排的一部分，像电影配乐——等你注意到它时，它可能已经越界了。*
