---
title: "代码之美：高亮、复制和语言标签"
date: 2026-04-11T09:00:00+08:00
description: "幻梦的代码块增强——Chroma语法高亮配色、语言标签自动识别、一键复制按钮、表格包装和引用块样式。"
tags: ["代码高亮", "Chroma", "语法", "复制", "Markdown"]
categories: ["功能特性"]
image: "https://t.alcy.cc/ycy?random=9"
---

## 技术博客的核心：代码块

对于一个技术博客，代码块是阅读体验的核心组成部分。糟糕的代码展示——没有语法高亮、行距过密、字体不清晰——会让读者关掉页面。

幻梦从四个维度优化代码块：语法高亮、语言标签、一键复制、视觉风格。

## Chroma 语法高亮

Hugo 内置 Chroma 高亮引擎。配置：

```toml
[markup.highlight]
  codeFences = true
  guessSyntax = true     # 自动检测语言
  noClasses = false      # 使用 CSS 类名而非内联样式
  tabWidth = 2
  lineNos = false
```

`noClasses = false` 是关键——它让 Chroma 生成 CSS 类名而非内联 `style`，让我们可以在 SCSS 中完全控制高亮的颜色方案。

在 `main.scss` 中为 Chroma 的每个 token 类型定义了颜色：

```scss
.highlight .k  { color: var(--csyn-kw); }   // keyword
.highlight .s  { color: var(--csyn-str); }  // string
.highlight .c  { color: var(--csyn-cm); }   // comment
.highlight .nf { color: var(--csyn-fn); }   // function
```

浅色和深色模式各有一套配色，通过 `[data-theme]` 切换。

## 语言标签

JavaScript 动态检测代码块的语言并添加标签：

```typescript
class EnhancementManager {
  addLanguageLabel(block: HTMLElement): void {
    const lang = block.className.match(/language-(\w+)/)?.[1];
    if (lang) {
      const label = document.createElement('span');
      label.className = 'code-lang';
      label.textContent = lang.toUpperCase();
      block.prepend(label);
    }
  }
}
```

标签显示在代码块右上角，用半透明小字标识语言（如 `JS`、`TS`、`PY`）。

## 一键复制

每个代码块右上角有一个复制按钮，默认隐藏，hover 代码块时显示：

```typescript
async copyCode(block: HTMLElement): void {
  const code = block.querySelector('code')?.textContent || '';
  try {
    await navigator.clipboard.writeText(code);
    this.showCopiedFeedback(button);  // "已复制！" → 2秒后消失
  } catch {
    // 回退方案：选中文本
    this.fallbackCopy(code);
  }
}
```

Clipboard API 是现代浏览器的标准能力，但也为旧浏览器准备了 `document.execCommand('copy')` 回退。

## 视觉风格

代码块使用 JetBrains Mono 等宽字体，圆角边框，半透明背景，与页面的玻璃态风格统一。深色模式下代码块背景比普通卡片略深，形成层次对比。

---

*好的代码展示就像好的排版——读者不会注意到它，但一旦缺失，立刻感觉哪里不对。*
