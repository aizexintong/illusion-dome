---
title: "国际化设计：一个YAML文件统一全站语言"
date: 2026-04-05T15:00:00+08:00
description: "幻梦的 i18n 国际化系统——Hugo 模板端和 JavaScript 运行时共享同一套翻译数据，~200项覆盖所有界面文字。"
tags: ["i18n", "国际化", "翻译", "YAML", "JavaScript"]
categories: ["功能特性"]
image: "https://t.alcy.cc/ycy?random=4"
---

## 不只是翻译文件

大部分 Hugo 主题的 i18n 仅仅是一个翻译字典。幻梦把它提升了一层：同一份翻译数据同时服务于 Hugo 模板和浏览器端的 JavaScript。

## Hugo 模板端

模板中通过 `i18n` 函数引用翻译：

```go-html-template
<span>{{ i18n "post.readingTime" .ReadingTime }}</span>
```

`zh-CN.yaml` 中定义：

```yaml
post:
  readingTime: "{{ . }} 分钟阅读"
```

`{{ . }}` 是 Go template 的上下文变量，会被替换为实际的阅读分钟数。

## JavaScript 运行时

问题在于：客户端 JavaScript 也需要显示翻译文字，但它访问不到 Hugo 的 i18n 系统。

解决方案：`baseof.html` 在页面渲染时将全部 i18n 数据注入为 JavaScript 变量：

```go-html-template
<script>
  window.i18nData = {{ dict "all" .Site.Data.i18n | jsonify }};
</script>
```

`main.ts` 中的 I18nHelper 模块：

```typescript
class I18nHelper {
  t(key: string, params?: Record<string, string>): string {
    const template = this.deepGet(window.i18nData, key);
    if (!template) {
      console.warn(`[i18n] Missing key: ${key}`);
      return key;
    }
    return this.interpolate(template, params);
  }

  interpolate(template: string, params?: Record<string, string>): string {
    if (!params) return template;
    return template.replace(/\{\{(\w+)\}\}/g, (_, key) => params[key] || '');
  }
}
```

## ~200 个翻译键

`zh-CN.yaml` 包含约 200 个翻译条目，覆盖：

| 类别 | 示例 |
|------|------|
| 导航 | 文章、归档、标签、关于 |
| 文章元数据 | 发布时间、修改时间、阅读时间、字数 |
| 分页 | 上一页、下一页、第X页 |
| 搜索 | 搜索文章、暂无结果、输入关键词 |
| 主题切换 | 浅色模式、深色模式、跟随系统 |
| 归档 | X年X月、全部文章 |
| 404 | 页面未找到、返回首页 |
| 无障碍 | 跳转到内容、关闭菜单 |
| 页脚 | 版权信息、运行时间 |
| 相对时间 | 刚刚、X分钟前、X小时前、X天前 |

## 相对时间的优雅实现

```yaml
relativeTime:
  justNow: "刚刚"
  minutesAgo: "{{.}} 分钟前"
  hoursAgo: "{{.}} 小时前"
  daysAgo: "{{.}} 天前"
  weeksAgo: "{{.}} 周前"
  monthsAgo: "{{.}} 个月前"
  yearsAgo: "{{.}} 年前"
```

模板和 JS 共用同一套文本，修改一处就全局生效。

---

*真正的国际化，是让每一种语言的用户都感觉这是为他们专门设计的。*
