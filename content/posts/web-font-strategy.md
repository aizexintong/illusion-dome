---
title: "Web字体加载策略：从Flash到Fancy"
date: 2026-04-27T10:00:00+08:00
description: "幻梦的字体加载方案——Inter、Noto Sans SC、JetBrains Mono的加载策略、FOUT vs FOIT、font-display和本地字体回退。"
tags: ["字体", "性能", "排版", "加载", "CSS"]
categories: ["技术分享"]
image: "https://t.alcy.cc/ycy?random=25"
---

## 字体加载的经典问题

Web 字体的加载有一个经典问题：在字体文件下载完成之前，浏览器应该显示什么？

- **FOIT (Flash of Invisible Text)**：文字不可见，直到字体加载完
- **FOUT (Flash of Unstyled Text)**：先用系统字体显示，加载完再切换

## font-display 解决方案

幻梦使用 `font-display: swap` 策略：

```css
@font-face {
  font-family: 'Noto Sans SC';
  src: local('Noto Sans SC'), local('PingFang SC'), local('Microsoft YaHei');
  font-display: swap;
}
```

`swap` 意味着：立即用后备字体显示文本，Web 字体加载完成后无缝切换。这是对可读性最友好的策略——用户始终能看到文字。

## 本地字体优先

```css
src: local('Noto Sans SC'),
     local('PingFang SC'),
     local('Microsoft YaHei');
```

`local()` 检查用户系统是否已安装这些字体。如果已安装，完全不需要下载。对于中文用户来说，系统很可能已内置这些字体。

## 三种字体的角色

| 字体 | 用途 | 字符集 |
|------|------|--------|
| Inter | 正文拉丁字符 | 拉丁扩展 |
| Noto Sans SC | 正文中文 | 中日韩 |
| JetBrains Mono | 代码块 | 等宽 |

## 字体回退链

```css
:root {
  --ff-body: 'Inter', 'Noto Sans SC', -apple-system, BlinkMacSystemFont,
             'Segoe UI', 'PingFang SC', 'Microsoft YaHei', sans-serif;
  --ff-code: 'JetBrains Mono', 'Cascadia Code', 'Fira Code',
             'Consolas', 'Monaco', monospace;
}
```

6 层回退确保在任何平台、任何字体加载状态下都有合适的字体。从 Web 字体 → 系统字体 → 通用字体族。

## 预连接 CDN

```html
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
```

提前建立到 Google Fonts 的连接，减少 SSL 握手和 DNS 解析的时间。

## 装饰字体的选择

```css
--ff-decorative: 'ZCOOL QingKe HuangYou', cursive;
```

站酷庆科黄油体作为装饰字体，仅用于个别强调文字（如首页的 Greeting），通过 `local()` 优先检查本地安装。

---

*字体加载是一门平衡术：加载太快（系统字体），页面没有个性；加载太慢，用户看着白屏离开。swap 策略是这之间的最佳支点。*
