---
title: "数学公式：在幻梦中书写 LaTeX"
date: 2026-10-22T10:00:00+08:00
description: "幻梦现已内置 KaTeX 数学公式渲染：行内公式、独立公式、矩阵、多行推导开箱即用，基于 Markdown passthrough 与 KaTeX auto-render，按需加载。"
summary: "幻梦内置 KaTeX 公式渲染，支持行内与独立公式、矩阵、多行对齐，按需加载。"
tags: ["LaTeX", "KaTeX", "数学公式", "Markdown"]
categories: ["功能特性"]
image: "https://t.alcy.cc/ycy?random=12"
math: true
---

## 写公式，不该是体力活

对数学、物理、算法类文章来说，公式是正文的一部分。过去在幻梦里写公式，只能手动引入 KaTeX 脚本；现在主题已内置 [KaTeX](https://katex.org/) 渲染链路，只要在 Markdown 里写下 LaTeX，剩下的交给主题。

## 两种写法，随手可用

行内公式直接嵌在句子里：质能方程 $E = mc^2$，欧拉恒等式 $e^{i\pi} + 1 = 0$，二项式系数 $\binom{n}{k}$。

独立公式单独成段居中显示：

$$
\int_{-\infty}^{+\infty} e^{-x^2}\,\mathrm{d}x = \sqrt{\pi}
$$

Hugo 的 Markdown 解析器会原样保留 `$...$`、`$$...$$`、`\(...\)`、`\[...\]` 之间的内容，再由 KaTeX 在浏览器里完成排版。所以公式里的下划线、星号、反斜杠不会被误当成 Markdown 语法。

## 复杂一点也没问题

矩阵与行列式：

$$
\mathbf{A} = \begin{pmatrix}
a_{11} & a_{12} & a_{13} \\
a_{21} & a_{22} & a_{23} \\
a_{31} & a_{32} & a_{33}
\end{pmatrix},
\qquad
\det(\mathbf{A}) = \sum_{\sigma \in S_3} \operatorname{sgn}(\sigma) \prod_{i=1}^{3} a_{i,\sigma(i)}
$$

多行对齐的推导，用 `aligned` 环境：

$$
\begin{aligned}
\nabla \cdot \mathbf{E} &= \frac{\rho}{\varepsilon_0} \\
\nabla \cdot \mathbf{B} &= 0 \\
\nabla \times \mathbf{E} &= -\frac{\partial \mathbf{B}}{\partial t} \\
\nabla \times \mathbf{B} &= \mu_0 \mathbf{J} + \mu_0 \varepsilon_0 \frac{\partial \mathbf{E}}{\partial t}
\end{aligned}
$$

四个方程写尽电磁世界——麦克斯韦方程组大概是物理学里最优雅的排版。

## 在代码块里，公式保持原样

公式渲染不会碰代码块，写教程时可以放心展示源码：

```latex
$$
\frac{\partial u}{\partial t} = \alpha \nabla^2 u
$$
```

## 一行配置的事

主题默认开启公式渲染，站点配置中也已包含保留 LaTeX 原文的 passthrough 规则。若想整体关闭，可在 `hugo.toml` 中设置：

```toml
[params]
  enableMath = false
```

也可以只对单篇文章关闭：

```yaml
---
title: "不需要公式的文章"
math: false
---
```

## 一点排版建议

- 独立公式前后留空行，避免和正文粘在一起；
- 行内公式尽量短，长公式拆成独立公式；
- 正文里出现价格等成对美元符号时，建议避免成对书写；行内公式也可统一改用 `\(...\)` 定界符（或在站点配置中移除 `$` 定界符）。

公式是另一种语言，排版是它的语法。幻梦能做的，是让你在写下它的那一刻，不必分心。

---

*数学是宇宙的语言，而 LaTeX 是我们与它对话的方式。*
