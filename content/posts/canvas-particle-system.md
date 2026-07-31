---
title: "粒子之舞：Canvas 粒子特效系统全解析"
date: 2026-04-06T09:00:00+08:00
description: "幻梦主题的核心视觉特色——Canvas 粒子特效，从技术实现到性能优化，详细解读昼夜双模式的粒子渲染系统。"
tags: ["Canvas", "JavaScript", "动画", "粒子特效", "性能优化"]
categories: ["功能特性"]
image: "https://t.alcy.cc/ycy?random=18"
---

## 不止是背景装饰

打开幻梦主题时，你会注意到背景中有飘落的粒子。这不是 gif 图，也不是视频循环，而是一个完整的 Canvas 粒子系统。

之所以选择 Canvas 而非 CSS 动画，有三个原因：
1. **粒子数量**：CSS 动画无法高效处理 50+ 个独立运动的粒子
2. **物理模拟**：Canvas 允许为每个粒子编写独立的行为逻辑
3. **性能可控**：可以根据设备性能动态调整粒子数量

## 系统架构

```
EffectsManager (主控)
├── 检测当前主题模式
├── 检测设备性能等级
├── DayModeRenderer     (浅色: 糖果雨)
│   ├── RibbonParticle  (彩带 x 15)
│   ├── CandyParticle   (3种糖果形状 x 12)
│   ├── HeartParticle   (爱心 x 5)
│   └── StarParticle    (星星 x 8)
└── NightModeRenderer   (深色: 星空)
    ├── StarParticle    (3种光谱 x 60)
    ├── FireflyParticle (萤火虫 x 8)
    └── ShootingStar    (流星 x 周期性)
```

## 日间模式：糖果雨

白天模式的灵感很偶然——写代码写到下午，桌上一颗水果糖在阳光下闪闪发光。于是有了糖果雨的构想。

### 糖果形状

糖果粒子有三种几何形态：

```typescript
function drawCandy(ctx: CanvasRenderingContext2D, x: number, y: number, type: number) {
  switch(type) {
    case 0: // 圆形软糖 - 径向渐变填充
      const gradient = ctx.createRadialGradient(x, y, 0, x, y, 6);
      gradient.addColorStop(0, '#FFB3C1');
      gradient.addColorStop(1, '#FF4D6D');
      ctx.arc(x, y, 6, 0, Math.PI * 2);
      break;
    case 1: // 方形奶糖 - 圆角矩形 + 高光
      roundRect(ctx, x-4, y-4, 8, 8, 2);
      break;
    case 2: // 星形硬糖 - 5角星路径
      drawStar(ctx, x, y, 5, 5, 2.5);
      break;
  }
}
```

### 彩带飘舞

彩带用二次贝塞尔曲线绘制，通过控制点的正弦波动模拟飘舞：

```typescript
// 每条彩带的控制点随时间正弦波动
const cpX = x + Math.sin(time * 0.02 + offset) * 20;
const cpY = y + Math.cos(time * 0.03 + offset) * 15;
ctx.quadraticCurveTo(cpX, cpY, endX, endY);
```

## 夜间模式：星空与萤火虫

深色模式下，粒子系统完全变样。糖果雨停歇，夜空降临。

### 三色光谱星星

不是所有星星都是白的。真实夜空中的恒星有不同的光谱类型：

```typescript
const starSpectra = [
  { color: '#E8E4F5', size: [1, 3], twinkle: 0.02 }, // 蓝白星 (A型)
  { color: '#FFF8E0', size: [2, 4], twinkle: 0.03 }, // 暖白星 (F型)
  { color: '#FFD4A0', size: [2.5, 5], twinkle: 0.04 }, // 橙星 (K型)
];
```

每颗星还有独立的闪烁周期和幅度，用正弦函数叠加随机偏移模拟自然闪烁。

### 萤火虫算法

萤火虫是夜间模式最迷人的部分。它们不是随机移动——那会显得机械。萤火虫的行为模拟了自然界中的"莱维飞行"模式：

```typescript
class Firefly {
  wander(): void {
    // 大部分时间缓慢漂浮
    this.x += Math.sin(this.phase) * 0.3;
    this.y += Math.cos(this.phase * 1.3) * 0.2;

    // 偶尔突然加速，模拟萤火虫的脉冲式移动
    if (Math.random() < 0.02) {
      this.vx = (Math.random() - 0.5) * 3;
      this.vy = (Math.random() - 0.5) * 3;
    }

    // 光晕脉冲
    this.glow = 0.3 + Math.sin(this.phase * 2) * 0.2;
  }
}
```

绿色的光晕配合半透明圆形，在深色背景上像极了夏夜里的萤火虫。

### 流星

每 15-30 秒，一颗流星划过天空，留下渐隐的尾迹。

## 性能自适应

不是每台设备都能流畅运行 80 个粒子：

```typescript
function detectPerformance(): number {
  const cores = navigator.hardwareConcurrency || 4;
  const memory = (navigator as any).deviceMemory || 4;
  const isMobile = /Mobi|Android/i.test(navigator.userAgent);

  if (cores <= 2 || memory <= 2 || isMobile) return 0.5;
  if (cores <= 4 || memory <= 4) return 0.75;
  return 1.0;
}
```

低端设备粒子减半。同时尊重 `prefers-reduced-motion` 用户偏好，完全禁用。

## Canvas 优化技巧

1. **离屏 Canvas**：静态背景星预渲染，避免每帧重绘
2. **requestAnimationFrame 节流**：低端设备降至 30fps
3. **对象池**：粒子对象复用，避免 GC 抖动
4. **ResizeObserver 防抖**：250ms 延迟避免连续重建

---

*粒子虽小，却承载着一个主题的气质。它们不是"特效"，是这个数字空间的天气。*
