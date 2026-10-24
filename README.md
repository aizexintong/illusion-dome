# 幻梦 Illusion - 演示站点

> 基于 [幻梦 Illusion](https://github.com/aizexintong/illusion) 主题搭建的 Hugo 个人博客演示站点

[![Hugo](https://img.shields.io/badge/Hugo-%3E%3D0.146.0-FF4088?logo=hugo)](https://gohugo.io)
[![Theme](https://img.shields.io/badge/Theme-Illusion%20v1.1.0-orange)](https://github.com/aizexintong/illusion)
[![Demo](https://img.shields.io/badge/Demo-illusion.azxt.org-brightgreen)](https://illusion.azxt.org)
[![License](https://img.shields.io/badge/License-GPLv3-blue.svg)](LICENSE)

**在线演示**: [https://illusion.azxt.org](https://illusion.azxt.org)

---

## 关于本仓库

本仓库是幻梦 (Illusion) 主题的**完整演示项目**，展示了主题的所有功能和页面。你可以将此仓库作为模板，快速搭建自己的博客。

### 已启用的功能

- **主题**: 幻梦 Illusion v1.1.0（正式预览版）
- **评论系统**: [Giscus](https://giscus.app/)（基于 GitHub Discussions），已配置并开启
- **搜索**: 本地客户端搜索，Ctrl+K 快捷键
- **粒子特效**: Canvas 糖果雨 / 星空萤火虫双模式
- **打字机**: Typed.js 首页轮播文案
- **数学公式**: 内置 KaTeX（LaTeX）渲染，行内 `$...$` 与独立 `$$...$$` 开箱即用
- **所有页面**: 首页、文章、归档、标签、关于、技能、友链、工具

---

## 快速开始

```bash
# 克隆仓库（包含主题子模块）
git clone --recursive https://github.com/aizexintong/illusion-dome.git
cd illusion-dome

# 本地运行
hugo server

# 访问 http://localhost:1313
```

如果未使用 `--recursive`，需手动拉取主题：

```bash
git submodule update --init --recursive
```

---

## 项目结构

```
illusion-dome/
├── hugo.toml              # 站点配置（包含 Giscus 评论配置）
├── data/
│   └── theme.yaml         # 主题数据（覆盖主题默认值）
├── i18n/
│   └── zh-CN.yaml         # 翻译文件（覆盖主题默认值）
├── content/
│   ├── posts/             # 文章
│   ├── _index.md          # 首页
│   ├── about.md           # 关于页
│   ├── archives.md        # 归档页
│   ├── links.md           # 友链页
│   ├── skills.md          # 技能页
│   ├── tools.md           # 工具页
│   ├── privacy.md         # 隐私政策
│   └── terms.md           # 使用条款
├── static/                # 静态资源（覆盖主题）
├── assets/                # 自定义样式/脚本（覆盖主题）
├── layouts/               # 自定义模板（覆盖主题）
└── themes/
    └── illusion/          # 幻梦主题（Git 子模块）
```

---

## 评论系统配置

本演示站点使用 **Giscus** 评论系统，基于 GitHub Discussions。在 `hugo.toml` 中的配置如下：

```toml
[params]
  enableComments = true
  commentsProvider = "giscus"

  giscusRepo = "aizexintong/illusion-dome"
  giscusRepoId = "R_kgDOSO5KPg"
  giscusCategory = "General"
  giscusCategoryId = "DIC_kwDOSO5KPs4DCigJ"
  giscusMapping = "pathname"
  giscusStrict = "0"
  giscusReactions = "1"
  giscusEmitMetadata = "0"
  giscusInputPosition = "bottom"
  giscusLang = "zh-CN"
```

主题还支持 Utterances、Disqus、Twikoo、Waline、Artalk 等评论系统，详见主题文档。

---

## 自定义你的站点

1. **修改站点信息**: 编辑 `hugo.toml`，修改 `baseURL`、`title`、`author`、社交链接等
2. **修改页面内容**: 编辑 `data/theme.yaml`，替换首页、关于、技能等页面数据
3. **修改翻译文本**: 编辑 `i18n/zh-CN.yaml`，覆盖任意 UI 文字
4. **添加文章**: 在 `content/posts/` 目录创建 Markdown 文件
5. **更换评论系统**: 编辑 `hugo.toml` 中 `commentsProvider` 及相关配置
6. **部署**: 运行 `hugo --minify` 后部署到 GitHub Pages / Vercel / Cloudflare Pages 等

---

## 主题

本仓库使用的主题仓库: [aizexintong/illusion](https://github.com/aizexintong/illusion)

主题特性包括三模主题切换、Canvas 粒子特效、毛玻璃 UI、AOS 滚动动画、本地搜索、KaTeX 公式渲染等，详见主题 README。

---

## 许可证

本仓库基于 **GPL v3.0** 许可证开源。详见 [LICENSE](LICENSE)。
