---
title: "TypeScript在Hugo中：js.Build实战"
date: 2026-04-24T11:00:00+08:00
description: "幻梦使用Hugo的js.Build编译TypeScript——零配置、esbuild引擎、IIFE格式、以及如何用TypeScript写出可维护的主题脚本。"
tags: ["TypeScript", "Hugo", "esbuild", "js.Build", "JavaScript"]
categories: ["技术分享"]
image: "https://t.alcy.cc/ycy?random=21"
---

## Hugo 可以编译 TypeScript？

是的。Hugo 0.74+ 内置了 `js.Build`，底层使用 esbuild。不需要 webpack、不需要 tsconfig、不需要任何 npm 依赖。

## 配置方式

```go-html-template
{{- $js := resources.Get "ts/main.ts" | js.Build (dict
  "target" "es2015"
  "format" "iife"
) -}}
{{- if hugo.IsProduction }}
  {{- $js = $js | minify | fingerprint }}
{{- end -}}
<script src="{{ $js.RelPermalink }}" defer></script>
```

三个关键参数：
- `target: "es2015"` — 编译到 ES6 语法，覆盖所有现代浏览器
- `format: "iife"` — 立即执行函数表达式，避免全局变量污染
- `defer` — 异步加载，不阻塞页面渲染

## IIFE 包裹后的输出

```javascript
(() => {
  // 全部 1800 行代码在这里
  // 不污染全局作用域
  class ThemeManager { ... }
  class EffectsManager { ... }
  function initIllusionTheme() { ... }
  initIllusionTheme();
})();
```

## 为什么是 TypeScript？

1800 行 JavaScript 如果没有类型检查，一个拼写错误可能要调试半小时。TypeScript 的静态类型分析在编译时就能捕获这些问题：

```typescript
interface SearchEntry {
  title: string;
  permalink: string;
  date: string;
  content: string;
  tags: string[];
}

class SearchEngine {
  private index: SearchEntry[] = [];

  search(query: string): SearchResult[] {
    // 编辑器自动补全 index 的每个字段
    // 类型错误在保存时就被 esbuild 捕获
  }
}
```

## 模块化开发

虽然是单体文件，但 TypeScript 的 `class` 语法天然支持逻辑分离：

```typescript
// 12 个管理器，每个都是独立的 class
class ThemeManager { ... }
class EffectsManager { ... }
class AnimationManager { ... }
class InteractionManager { ... }
class EnhancementManager { ... }
class UtilsManager { ... }
class SearchEngine { ... }
class CalendarWidget { ... }
class TagsPagination { ... }
class ArchivesNavigator { ... }
class FooterManager { ... }
class I18nHelper { ... }

// 统一的初始化入口
function initIllusionTheme(): void {
  if ((window as any).__illusionInitialized) return;
  (window as any).__illusionInitialized = true;

  const i18n = new I18nHelper();
  const theme = new ThemeManager();
  const effects = new EffectsManager(theme);
  const animation = new AnimationManager();
  const interaction = new InteractionManager();
  const enhancement = new EnhancementManager();
  const utils = new UtilsManager();
  const search = new SearchEngine();
  const calendar = new CalendarWidget(i18n);
  const tagsPagination = new TagsPagination();
  const archivesNav = new ArchivesNavigator();
  const footer = new FooterManager();
}
```

每个管理器有清晰的职责边界，修改粒子特效不会影响搜索功能。

## esbuild vs webpack

| | esbuild (Hugo) | webpack |
|---|---|---|
| 编译速度 | < 50ms | 2-10s |
| 配置文件 | 0 | webpack.config.js |
| TypeScript | 原生支持 | 需要 ts-loader |
| 打包 | 单文件 IIFE | 复杂分块策略 |
| 适用场景 | Hugo 主题脚本 | 大型 SPA 应用 |

对于 Hugo 主题来说，esbuild 的速度优势是压倒性的。

---

*在一个没有 node_modules 的 Hugo 项目中写 TypeScript——这是我对"简洁"的理解。*
