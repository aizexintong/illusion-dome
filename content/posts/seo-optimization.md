---
title: "博客SEO：从Open Graph到结构化数据"
date: 2026-04-04T10:00:00+08:00
description: "幻梦主题内置的SEO优化方案——Open Graph、Twitter Cards、Schema.org结构化数据、RSS和Sitemap的完整实现。"
tags: ["SEO", "Open Graph", "Schema.org", "RSS", "优化"]
categories: ["功能特性"]
image: "https://t.alcy.cc/ycy?random=3"
---

## SEO不只是关键词

很多开发者以为 SEO 就是往页面里塞关键词。实际上，现代 SEO 的核心是**让搜索引擎和社交平台理解你的内容结构**。

幻梦从三个层面做了优化：社交分享预览、搜索引擎结构化数据、内容分发。

## Open Graph & Twitter Cards

当你的文章被分享到 Twitter、Facebook、Telegram 等平台时，这些协议控制着链接卡片的显示效果。

幻梦在 `<head>` 中自动生成：

```html
<meta property="og:title" content="文章标题" />
<meta property="og:description" content="文章描述" />
<meta property="og:image" content="https://yourdomain.com/og-image.jpg" />
<meta property="og:url" content="https://yourdomain.com/posts/slug/" />
<meta property="og:type" content="article" />
<meta name="twitter:card" content="summary_large_image" />
```

优先级：文章封面图 > 主题默认 OG 图 > 站点头像。

## Schema.org 结构化数据

结构化数据帮助搜索引擎理解页面内容类型。幻梦自动注入 JSON-LD：

```json
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "爱则心痛",
  "url": "https://azxt.org",
  "logo": "https://azxt.org/headimg.jpg"
}
```

文章页额外注入 `Article` 类型，包含发布时间、修改时间、作者信息等。

## RSS Feed

RSS 仍然是最可靠的独立内容分发方式。Hugo 原生支持，无需额外配置：

```toml
[outputs]
  home = ["HTML", "JSON", "RSS"]
  section = ["HTML", "RSS"]
```

## Sitemap

自动生成的站点地图帮助搜索引擎发现页面：

```xml
<sitemapindex>
  <sitemap>
    <loc>https://azxt.org/sitemap.xml</loc>
  </sitemap>
</sitemapindex>
```

## 减少搜索结果的文字

一个容易被忽略的细节：页面描述（description）的写法直接影响搜索结果的外观。幻梦的文章摘要会自动截取 Front Matter 中的 `description` 字段，控制在 160 字符以内——这是大多数搜索引擎显示摘要的长度上限。

---

*好的 SEO 让机器理解你的内容，而好的内容留住被吸引来的人。*
