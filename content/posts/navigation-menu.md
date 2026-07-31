---
title: "导航设计：站点地图的艺术"
date: 2026-04-18T16:00:00+08:00
description: "幻梦的导航系统——桌面端水平菜单、移动端全屏覆盖、面包屑逻辑、以及如何让用户永远知道自己在哪。"
tags: ["导航", "菜单", "UX", "移动端", "交互"]
categories: ["设计理念"]
image: "https://t.alcy.cc/ycy?random=15"
---

## 导航是站点的骨架

如果内容是血肉，导航就是骨架。一个好的导航系统让用户永远知道三件事：我在哪、我能去哪、怎么去。

## 桌面端：水平导航

```go-html-template
{{ partial "_layout/menu.html" (dict "menuID" "main" "page" .) }}
```

菜单渲染为水平链接列表，每个链接带 FontAwesome 图标。当前页面高亮（`aria-current="page"`）。

导航栏使用 `position: sticky`，随页面滚动始终可见。滚动时通过 JS 添加 `.scrolled` 类，背景不透明度从 0.72 提升到 0.92：

```typescript
window.addEventListener('scroll', () => {
  const scrolled = window.scrollY > 10;
  header.classList.toggle('scrolled', scrolled);
});
```

## 移动端：全屏覆盖

小屏幕上，水平导航隐藏，汉堡菜单出现。点击后打开从右侧滑入的全屏菜单面板：

```scss
.mobile-menu-panel {
  position: fixed;
  top: 0;
  right: -300px;
  width: 280px;
  height: 100vh;
  background: var(--cbg1);
  backdrop-filter: blur(24px);
  transition: right 0.3s ease;
  z-index: 998;

  &.active { right: 0; }
}

.mobile-menu-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  opacity: 0;
  pointer-events: none;
  transition: opacity 0.3s ease;

  &.active {
    opacity: 1;
    pointer-events: auto;
  }
}
```

菜单面板底部显示社交链接（GitHub、Email、RSS），让移动端不需要翻到页脚就能找到联系方式。

## 菜单渲染器

`menu.html` partial 是通用菜单渲染器，接收 `menuID` 参数：

```go-html-template
<ul>
  {{ range index .page.Site.Menus .menuID }}
    <li>
      <a href="{{ .URL }}" {{ if $.page.IsMenuCurrent .Menu . }}aria-current="page"{{ end }}>
        {{ with .Params.icon }}<i class="{{ . }}"></i>{{ end }}
        <span>{{ .Name }}</span>
      </a>
    </li>
  {{ end }}
</ul>
```

通过 `menuID` 参数区分主菜单和页脚菜单，一套逻辑渲染所有导航。

## 页脚导航

页脚包含快速链接（主菜单的紧凑版）和技术栈预览（前 6 个技能）。四列网格布局：

- 第一列：关于信息
- 第二列：快速链接
- 第三列：技术栈
- 第四列：关注我（社交链接 + 座右铭）

## 两种菜单的数据来源

```toml
[menus]
  [[menus.main]]     # 主导航
  [[menus.footer]]   # 页脚导航
```

都在 `hugo.toml` 中配置，修改方便。

---

*导航不是让用户"找到路"，而是让用户"感觉不到在找路"。*
