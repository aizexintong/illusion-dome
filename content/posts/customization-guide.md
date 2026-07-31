---
title: "幻梦定制指南：打造属于你的独一无二的博客"
date: 2026-04-20T13:00:00+08:00
lastmod: 2026-07-31T16:00:00+08:00
description: "从配置到样式到模板，全面介绍如何将幻梦主题定制为属于你自己的样子。不需要了解 Go 模板语法也能轻松上手。"
tags: ["教程", "定制", "配置", "幻梦"]
categories: ["使用指南"]
image: "https://t.alcy.cc/ycy?random=42"
---

## 个性化从 YAML 开始

幻梦的核心理念是：**内容定制不需要碰代码。** 你只需要编辑一个文件——站点根目录下的 `data/theme.yaml`。

如果你还没有这个文件，从主题复制一份：

```bash
cp themes/illusion/data/theme.yaml data/theme.yaml
```

现在，让我们从零开始打造一个属于你的博客。

## 第一步：修改基础信息

```yaml
home:
  hero:
    title: "你的名字"
    subtitles:                          # 首页打字机轮播
      - "全栈工程师"
      - "开源爱好者"
      - "摄影发烧友"
    greeting: "嗨，我是"
    badgeText: "欢迎来玩"
    primaryBtn:
      text: "看文章"
      url: "/posts"
    secondaryBtn:
      text: "关于我"
      url: "/about"
    quote:
      text: "在这里放一句你喜欢的话。"
      author: "—— 作者"
```

修改完后重启 `hugo server`，首页就变成你的了。

## 第二步：关于页

```yaml
about:
  intro:
    name: "你的名字 (yourname)"
    title: "做什么的 · 喜欢什么 · 追求什么"
    quote: "你的座右铭"
  stats:
    - icon: "fas fa-code"
      number: "auto"         # "auto" = 自动统计文章数
      label: "文章"
    - icon: "fas fa-calendar-alt"
      number: "3+"
      label: "经验"
    - icon: "fas fa-mug-hot"
      number: "∞"
      label: "咖啡因摄入"
    - icon: "fas fa-heart"
      number: "100%"
      label: "热爱"
  philosophy:
    title: "我的理念"
    items:
      - icon: "fas fa-feather-alt"
        title: "简洁"
        description: "少即是多"
  contact:
    title: "找到我"
    methods:
      - type: "email"
        icon: "fas fa-envelope"
        label: "邮件"
        value: "you@example.com"
        link: "mailto:you@example.com"
      - type: "github"
        icon: "fab fa-github"
        label: "GitHub"
        value: "yourname"
        link: "https://github.com/yourname"
```

## 第三步：技能页 & 友链页

```yaml
skills:
  overview:
    categories:
      - icon: "fas fa-code"
        title: "前端"
        description: "React, Vue, TypeScript"
        progress: 100
  skills:
    - name: "TypeScript"
      level: 90
      description: "擅长类型系统设计和大型项目架构"
      projects:
        - "企业级后台管理系统"
  timeline:
    - year: "2020"
      title: "入门编程"
      description: "写下了第一行 console.log('hello world')"

friends:
  friends:
    - name: "朋友A的博客"
      url: "https://friend-a.com"
      avatar: "https://t.alcy.cc/tx"
      description: "一个很酷的全栈开发者"
      category: "技术博客"
  apply:
    conditions:
      - "原创内容为主"
      - "定期更新"
    siteInfo:
      name: "你的博客名"
      url: "https://yourdomain.com"
      description: "一句话介绍"
```

## 第四步：社交链接 & 图片

```yaml
social:
  links:
    - platform: "github"
      url: "https://github.com/yourname"
      icon: "fab fa-github"
      label: "GitHub"
      show: true
    - platform: "email"
      url: "mailto:you@example.com"
      icon: "fas fa-envelope"
      label: "邮箱"
      show: true

assets:
  avatar:
    url: "/headimg.jpg"
  background:
    adaptive:
      url: "https://t.alcy.cc/ycy"
```

## 第五步：布局微调

```yaml
layout:
  posts:
    layout: "grid"          # grid | list
    columns: 2
    perPage: 12
    excerptLength: 160
  sidebar:
    position: "right"
    showTOC: true
    showInfo: true
    showTags: true
  codeblock:
    showLang: true
    showCopy: true
    lineNumbers: false
```

## 进阶定制

### 修改样式

在站点根目录创建 `assets/scss/custom.scss`，然后在站点 `layouts/partials/_layout/head/css.html` 中引入（覆盖主题同名 partial）。

```scss
// 改变主色调
:root {
  --cp1: #3498db;
  --cp5: #2980b9;
}
```

### 修改模板

```bash
# 复制主题模板到站点
cp themes/illusion/layouts/posts/single.html layouts/posts/single.html
# 编辑——站点文件会完全替代主题文件，升级不会覆盖
```

### 修改翻译

```bash
cp themes/illusion/i18n/zh-CN.yaml i18n/zh-CN.yaml
```

---

*幻梦给了你一套完整的画布和颜料。剩下的，就是你来画。*
