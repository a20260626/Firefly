---
title: "认识 Firefly 文件结构并修改配置"
published: 2026-07-12T18:00:00+08:00
updated: 2026-07-12T18:00:00+08:00
pinned: false
description: "认识 Firefly 的 Astro 项目结构，找到文章、页面、组件和配置文件，并完成一次安全的站点配置修改。"
tags: [AI编程, Firefly, Astro, 文件结构, 配置]
category: 博客指南
draft: false
---

完成 [把 Firefly 在本地运行起来](/posts/run-firefly-locally/) 后，下一步不是立刻修改很多文件，而是先建立一张“项目地图”。知道文件负责什么，再做范围明确的配置修改，遇到问题时才能快速定位。

## 问题是什么

本篇只解决一个问题：**如何在不破坏项目的前提下，找到 Firefly 的核心文件并修改一个站点配置。**

完成标准是：

- 能说出文章、页面、组件、布局、配置和静态资源分别放在哪里；
- 能从 `src/config/index.ts` 找到配置的统一出口；
- 能修改站点标题或副标题，并在本地页面看到变化；
- 修改后 `pnpm check` 和 `pnpm build` 都能通过。

本篇暂时不新增功能，不重写布局，也不同时修改多个配置模块。一次只验证一个变化，便于判断结果是否正确。

## 适用环境是什么

本文适用于已经完成以下准备的 Windows 开发环境：

- 已经 Fork 并克隆 Firefly 仓库；
- 已经在项目根目录成功执行过 `pnpm install`；
- Node.js 版本不低于 22，pnpm 可正常使用；
- 使用 VS Code 打开了包含 `package.json` 和 `pnpm-lock.yaml` 的项目根目录；
- 能够使用 `pnpm dev` 启动本地开发服务器。

本文以当前 Firefly 仓库为准。仓库升级后，个别文件名或目录可能变化，遇到差异时先查看根目录的 `README.md`、`src/config/README.md` 和 `package.json`。

## 具体怎么做

### 1. 先确认自己打开的是项目根目录

在 VS Code 的资源管理器中确认当前目录包含这些文件：

```text
Firefly/
├── package.json       # 项目脚本、依赖和包管理器信息
├── pnpm-lock.yaml     # 锁定依赖版本
├── astro.config.mjs   # Astro 构建配置
├── public/            # 原样复制到网站根目录的静态文件
├── scripts/           # 图标、图片、字体等构建脚本
└── src/               # 网站源代码和文章内容
```

如果看不到 `package.json`，说明当前目录可能是仓库的上一级或某个子目录。先在终端执行：

```bash
pwd
```

Windows PowerShell 也可以执行：

```powershell
Get-Location
```

### 2. 先认识 `src` 目录

Firefly 的主要开发内容集中在 `src`：

```text
src/
├── assets/       # 需要由 Astro 优化处理的图片等资源
├── components/   # 可复用的 Astro、Svelte 和其他界面组件
├── config/       # 站点功能和视觉效果配置
├── content/      # Markdown 文章和内容集合
├── layouts/      # 页面整体布局
├── pages/        # 页面路由，文件名对应网址路径
├── plugins/      # Markdown、代码块等内容处理插件
├── styles/       # 全局样式和主题样式
├── types/        # TypeScript 类型定义
└── utils/        # 通用工具函数
```

可以先记住四个最常用的位置：

| 目标 | 位置 |
| --- | --- |
| 新增文章 | `src/content/posts/` |
| 修改站点配置 | `src/config/` |
| 修改页面路由 | `src/pages/` |
| 修改公共界面 | `src/components/` 或 `src/layouts/` |

### 3. 认识文章和路由的关系

文章放在 `src/content/posts/`，例如：

```text
src/content/posts/
├── install-required-environment.md
├── fork-firefly-and-clone.md
├── run-firefly-locally.md
└── setup-project-foundation.md
```

Markdown 文件顶部的 frontmatter 决定标题、日期、分类和标签；文章正文负责页面内容。文章详情页的路由由文章文件名生成，例如：

```text
src/content/posts/run-firefly-locally.md
→ http://127.0.0.1:24321/posts/run-firefly-locally/
```

因此，文章内容通常不需要手动修改 `src/pages`。只有在要增加一种新的页面类型时，才需要进一步研究页面路由文件。

### 4. 认识 `src/config` 配置目录

Firefly 把配置按功能拆成多个文件，并通过 `src/config/index.ts` 统一导出。常用配置如下：

| 文件 | 主要用途 |
| --- | --- |
| `siteConfig.ts` | 站点标题、副标题、网址、主题色、分页和文章页开关 |
| `profileConfig.ts` | 头像、昵称、个人签名和社交链接 |
| `navBarConfig.ts` | 顶部导航菜单和外部链接 |
| `sidebarConfig.ts` | 左右侧栏位置及侧栏组件 |
| `backgroundWallpaper.ts` | 壁纸、横幅、渐变和水波纹动画 |
| `effectsConfig.ts` | 樱花等动画特效 |
| `commentConfig.ts` | 评论系统 |
| `footerConfig.ts` | 页脚内容 |

不确定某个配置在哪里时，先打开 `src/config/README.md`，再搜索配置名称。不要为了修改标题去改布局组件，也不要把多个功能配置混到同一个文件里。

### 5. 修改一个最小配置：站点副标题

打开 `src/config/siteConfig.ts`，找到：

```ts
export const siteConfig: SiteConfig = {

	title: "Steve",

	subtitle: "Demo site",
```

上面的代码展示了配置所在位置。将 `subtitle` 修改为自己的站点副标题，例如：

```ts
	subtitle: "AI 编程学习记录",
```

保持项目原有的 Tab 缩进和双引号格式即可。

如果还要修改站点名称，只修改同一个对象中的 `title`：

```ts
	title: "我的 Firefly 学习站",
```

一次先改一个字段。保存文件后，观察本地开发服务器和浏览器是否自动刷新。

### 6. 修改视觉效果时，先看对应配置

例如要默认关闭水波纹动画，打开 `src/config/backgroundWallpaper.ts`，确认 `common.waves` 使用：

```ts
waves: {
	enable: {
		desktop: false,
		mobile: false,
	},
	switchable: false,
},
```

这里的 `desktop` 和 `mobile` 控制默认状态，`switchable` 控制用户是否能在控制面板中重新切换。只想默认关闭但保留用户切换能力时，可以保留 `switchable: true`；希望访问每个页面都不出现水波纹动画时，使用 `switchable: false`。

### 7. 用开发服务器检查修改

如果开发服务器没有运行，在项目根目录执行：

```bash
pnpm dev
```

打开终端显示的 `Local` 地址。不要假设端口一定是 `24321`，因为端口可能已经被其他进程占用。刷新首页，确认浏览器标题或页面中使用站点副标题的位置已经更新。

### 8. 修改前后都查看 Git 差异

在终端执行：

```bash
git diff -- src/config/siteConfig.ts
git status --short
```

确认差异只有自己计划修改的配置字段。若出现大量无关文件变化，先暂停，不要直接提交。

## 如何验证完成

按顺序完成下面的检查：

- [ ] 当前目录能看到 `package.json` 和 `pnpm-lock.yaml`；
- [ ] 能指出文章目录 `src/content/posts/`；
- [ ] 能指出配置目录 `src/config/`；
- [ ] 能指出页面目录 `src/pages/`；
- [ ] 能在 `src/config/index.ts` 找到配置导出；
- [ ] 已修改 `siteConfig.ts` 中的一个字段；
- [ ] 已在本地浏览器打开终端显示的 `Local` 地址；
- [ ] 浏览器中能看到配置修改后的效果；
- [ ] `git diff` 中没有无关修改；
- [ ] `pnpm check` 执行成功；
- [ ] `pnpm build` 执行成功；
- [ ] 关闭开发服务器后，修改没有被误认为已经提交。

验证命令：

```bash
pnpm check
pnpm build
git diff --check
```

完成后，项目结构就不再是“看不懂的一堆文件”，而是一张可以按职责查找的地图。下一步可以继续学习如何 [新增一篇 Markdown 文章](/posts/write-first-code-with-ai/)，或者先通过 `git status --short` 检查当前改动。

## 失败时先检查什么

### 修改后页面没有变化

先确认修改的是 `src/config/siteConfig.ts`，并且保存了文件。再检查浏览器是否打开了当前项目的开发服务器地址，而不是 GitHub Pages 或其他本地项目。

### `pnpm check` 报 frontmatter 错误

检查 Markdown 文件顶部的 `---` 是否成对存在，日期是否使用合法格式，标签是否是数组。配置文件则检查引号、逗号和花括号是否完整。

### `pnpm build` 报 TypeScript 或配置错误

查看第一条错误的文件路径和行号，不要只看最后一行。回到最近一次修改的位置，逐项恢复或修正。可以把完整错误、修改前后的差异和 Node.js/pnpm 版本一起交给 AI 分析。

### 水波纹仍然出现

检查 `backgroundWallpaper.ts` 中桌面端和移动端是否都为 `false`，再检查 `switchable` 是否仍为 `true`。浏览器还可能保存了控制面板的 `localStorage` 状态，清除站点本地存储后重新打开页面。

### 不知道该改哪个文件

先描述目标，再搜索配置名称。例如“我要修改顶部菜单”对应 `navBarConfig.ts`，“我要修改侧栏组件”对应 `sidebarConfig.ts`，“我要修改文章内容”对应 `src/content/posts/`。不要根据文件名猜测后一次改很多文件。

---

*从零开始学 AI 编程 · 阶段五 · Firefly 实战*
