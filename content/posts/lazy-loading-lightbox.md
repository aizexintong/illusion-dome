---
title: "图片懒加载与灯箱：让图像加载更聪明"
date: 2026-04-12T11:00:00+08:00
description: "幻梦主题的图片处理方案——IntersectionObserver驱动的懒加载、点击放大的灯箱组件、响应式图片和加载占位符。"
tags: ["图片", "懒加载", "灯箱", "IntersectionObserver", "性能"]
categories: ["功能特性"]
image: "https://t.alcy.cc/ycy?random=10"
---

## 图片是页面最大的性能杀手

一张未经优化的封面图可能占用 2MB，而一篇 5000 字的文章只需要 15KB。图片的加载速度决定了页面的首屏时间。

幻梦用三种策略处理图片：懒加载、灯箱、响应式。

## 懒加载

浏览器原生的 `loading="lazy"` 已经很好，但幻梦额外使用 IntersectionObserver 做更精细的控制：

```typescript
class InteractionManager {
  private observer: IntersectionObserver;

  initLazyLoading(): void {
    this.observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          const img = entry.target as HTMLImageElement;
          if (img.dataset.src) {
            img.src = img.dataset.src;     // 替换真实 URL
            img.removeAttribute('data-src');
          }
          this.observer.unobserve(img);
        }
      });
    }, {
      rootMargin: '200px'   // 提前 200px 开始加载
    });

    document.querySelectorAll('img[data-src]').forEach(img => {
      this.observer.observe(img);
    });
  }
}
```

`rootMargin: '200px'` 让图片在进入视口前 200px 就开始加载，用户滚动时不会看到空白。

配合一个轻量的 SVG 占位符，在图片加载前保持布局稳定，避免页面跳动。

## 图片灯箱

点击文章中的图片时，弹出全屏灯箱：

```typescript
class UtilsManager {
  openLightbox(img: HTMLImageElement): void {
    const lightbox = document.createElement('div');
    lightbox.className = 'lightbox-overlay';

    const cloned = document.createElement('img');
    cloned.src = img.src;
    cloned.alt = img.alt;

    lightbox.appendChild(cloned);
    document.body.appendChild(lightbox);

    // 点击外部关闭
    lightbox.addEventListener('click', () => lightbox.remove());
    // Esc 关闭
    document.addEventListener('keydown', (e) => {
      if (e.key === 'Escape') lightbox.remove();
    }, { once: true });
  }
}
```

点击背景或按 Esc 关闭，过渡动画使用 CSS transition，200ms 淡入。

## 响应式图片

虽然 Hugo 本身对响应式图片的支持有限，但幻梦的 CSS 保证了图片在各种屏幕上不会溢出：

```css
.article-text img {
  max-width: 100%;
  height: auto;
  border-radius: var(--br1);
}
```

---

*图片加载的每个细节都影响用户体验。懒加载、灯箱、占位符——这些细节累积起来，就是专业和业余的差距。*
