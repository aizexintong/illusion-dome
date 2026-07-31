---
title: "中文排版：在像素中寻找呼吸感"
date: 2026-04-27T16:00:00+08:00
description: "幻梦主题的中文排版设计——字体选择策略、行高与字间距、中西文混排、以及为什么大多数英文主题无法直接适用于中文。"
tags: ["排版", "中文字体", "Typography", "设计", "可读性"]
categories: ["设计理念"]
image: "https://t.alcy.cc/ycy?random=25"
---

## 中文排版不是英文翻译

一个常见的误区：拿一个设计精美的英文博客主题，把文字翻译成中文，就以为大功告成。

但中文和英文是完全不同的排版系统。英文靠字母宽度变化建立节奏，中文的方块字需要完全不同的间距策略。大多数英文主题直接用在中文字体上会显得拥挤或松散。

## 字体选择

```css
:root {
  --ff-body: 'Inter', 'Noto Sans SC', 'PingFang SC', 'Microsoft YaHei', sans-serif;
  --ff-mono: 'JetBrains Mono', 'Consolas', 'SimHei', monospace;
}
```

三层回退策略：
1. **西文先匹配 Inter**（现代无衬线，清晰锐利）
2. **中文匹配 Noto Sans SC**（Google 开源，专为屏幕阅读优化）
3. **系统兜底**（PingFang SC — macOS / Microsoft YaHei — Windows）

## 行高

```css
body {
  line-height: 1.75;   /* 中文正文 */
}
```

中文方块字的视觉密度高于拉丁字母，需要更大的行高提供呼吸感。1.75 的行高在阅读舒适度和屏幕利用率之间取得了平衡。

相比之下，英文正文通常使用 1.5-1.6 的行高。

## 字间距

```css
body {
  letter-spacing: 0.02em;  /* 极微的间距 */
}
```

中文无需太大字间距——方块字自带结构边界。但 0.02em 的微妙间距让文字不过于拥挤。

## 中西混排

技术博客中经常中英文混排。当英文单词出现在中文段落时，如何在两种文字系统之间建立和谐？

```css
/* 英文在中文中略做调整 */
p {
  word-break: break-word;
  overflow-wrap: break-word;
}

code {
  font-family: var(--ff-mono);
  font-size: 0.9em;     /* 略小于正文 */
  padding: 0.15em 0.3em;
  border-radius: 3px;
}
```

内联代码使用比正文稍小的字号，与中文方块字形成视觉区分。

## 标题层级

```css
h1 { font-size: var(--fs-3xl); font-weight: 700; letter-spacing: -0.01em; }
h2 { font-size: var(--fs-2xl); font-weight: 600; }
h3 { font-size: var(--fs-xl);  font-weight: 600; }
h4 { font-size: var(--fs-lg);  font-weight: 500; }
```

标题和正文字号差距明显，视觉层次清晰。大标题略微收紧字间距（负值），给人更紧凑、有力的视觉印象。

## 段落间距

```css
p + p {
  margin-top: 0.8em;
}

h2 + p, h3 + p {
  margin-top: 0.5em;
}
```

段落之间有间距，标题和紧随的段落间距更小。这是排版中的"邻近原则"——相关的内容靠得更近。

## 引用块

```css
blockquote {
  border-left: 3px solid var(--ca4);
  padding-left: var(--s5);
  color: var(--ctsg);
  font-style: italic;
}
```

左边界使用主题强调色，与正文形成视觉区分。斜体给引用增加了"被引用"的语气感。

---

*排版不是"字好不好看"，是"读起来累不累"。好的排版让你忘记排版的存在。*
