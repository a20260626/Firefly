# Firefly 项目实现长提示词

本文档用于用长提示词描述 Firefly 项目的实现方式。它适合交给 AI 作为项目理解、复刻、重构或二次开发的上下文。

---

## 1. 项目定位

请实现一个名为 **Firefly** 的现代化个人博客主题项目。

项目应是一个高性能、可配置、视觉精致、内容增强能力丰富的静态博客系统。它既可以作为个人博客使用，也可以作为内容创作者、技术作者、作品展示站、知识库入口使用。

项目基于 **Astro 7** 构建，优先输出静态页面，保证 SEO、首屏性能和部署便利性。对于需要浏览器交互的部分，使用 **Svelte islands** 或少量客户端脚本实现，而不是把整个站点做成 SPA。

---

## 2. 技术栈

### 2.1 核心框架

- 使用 `Astro 7` 作为静态站点生成框架。
- 使用 `TypeScript` 作为主要开发语言。
- 使用 `Svelte 5` 实现交互式岛屿组件。
- 使用 `Tailwind CSS 4` 构建响应式样式系统。
- 使用 `pnpm` 管理依赖。

### 2.2 内容与构建工具

- 使用 `Astro Content Collections` 管理 Markdown/MDX 内容。
- 使用 `MDX` 支持文章中嵌入组件。
- 使用 `Pagefind` 生成全文搜索索引。
- 使用 `Satori` 和 `Sharp` 生成动态 Open Graph 图片。
- 使用 `Biome` 进行格式化和 lint。
- 使用 `astro-expressive-code` 渲染高级代码块。

### 2.3 部署适配

- 默认支持静态站点部署。
- 当存在 `CF_WORKERS` 环境变量时，启用 `@astrojs/cloudflare` adapter。
- 支持部署到 Vercel、Netlify、Cloudflare Pages、Cloudflare Workers 等平台。

---

## 3. 项目目录结构

项目采用清晰的分层结构。

```text
src/
  assets/       源图片与构建期可优化资源
  components/   可复用 UI 与功能组件
  config/       站点配置文件
  constants/    构建生成或全局常量
  content/      Markdown/MDX 内容
  i18n/         多语言文本
  layouts/      页面布局
  pages/        Astro 路由页面
  plugins/      Markdown/HTML 插件
  styles/       全局样式
  types/        类型定义
  utils/        工具函数
public/         静态公开资源
docs/           项目文档和截图
scripts/        构建、生成、自动化脚本
```

### 3.1 `src/pages`

`src/pages` 放置所有 Astro 路由页面。

应包含：

- 首页与分页列表：`[...page].astro`
- 文章详情页：`posts/[...slug].astro`
- 归档页：`archive.astro`
- 分类页：`categories/index.astro`
- 标签页：`tags/index.astro`
- 搜索页：`search.astro`
- 友链页：`friends.astro`
- 赞助页：`sponsor.astro`
- 留言页：`guestbook.astro`
- 相册页：`gallery/index.astro` 和 `gallery/[album].astro`
- 番剧/追番页：`bangumi.astro`
- 动画列表页：`anime.astro`
- RSS：`rss.astro` 和 `rss.xml.ts`
- robots：`robots.txt.ts`
- Open Graph 图片 API：`og/[...slug].ts`
- 文章元数据 API：`api/allPostMeta.json.ts`
- 404 页面：`404.astro`

### 3.2 `src/layouts`

`src/layouts` 负责页面骨架。

- `Layout.astro`：全局 HTML 文档、head、SEO、meta、favicon、全局样式、全局脚本、统计脚本。
- `MainGridLayout.astro`：主内容网格、导航栏、banner、左右侧栏、浮动目录、主体内容插槽。

### 3.3 `src/components`

组件按功能分组。

```text
components/
  analytics/  统计分析组件
  comment/    评论系统组件
  common/     通用组件
  controls/   控件组件
  features/   功能组件
  layout/     布局组件
  misc/       杂项组件
  pages/      页面级组件
  widget/     侧栏和小组件
```

组件应保持小而清晰。静态展示优先使用 Astro 组件，交互能力较强的部分使用 Svelte。

### 3.4 `src/config`

配置系统是 Firefly 的核心。

所有站点可变内容都应通过配置文件控制，而不是硬编码在组件中。

典型配置包括：

- `siteConfig.ts`：站点标题、描述、语言、分页、页面开关、主题色、文章设置。
- `profileConfig.ts`：作者名称、头像、简介、社交链接。
- `navBarConfig.ts`：导航栏、搜索入口、菜单项。
- `sidebarConfig.ts`：左右侧栏布局、小组件启用状态。
- `backgroundWallpaper.ts`：背景图、banner、全屏壁纸、透明模式、纯色模式。
- `coverImageConfig.ts`：文章封面图策略。
- `commentConfig.ts`：评论系统配置。
- `analyticsConfig.ts`：Google Analytics、Microsoft Clarity、Umami、51.LA 等统计配置。
- `musicConfig.ts`：音乐播放器配置。
- `galleryConfig.ts`：相册配置。
- `friendsConfig.ts`：友链配置。
- `sponsorConfig.ts`：赞助方式配置。
- `licenseConfig.ts`：文章许可协议配置。
- `fontConfig.ts`：字体选择和字体提供方配置。
- `expressiveCodeConfig.ts`：代码块主题和插件配置。
- `plantumlConfig.ts`：PlantUML 渲染配置。
- `pioConfig.ts`：Live2D/Spine 看板娘配置。
- `effectsConfig.ts`：樱花等特效配置。
- `footerConfig.ts` 和 `FooterConfig.html`：页脚配置和自定义 HTML。
- `announcementConfig.ts`：公告配置。

`src/config/index.ts` 应统一导出所有配置和类型，组件优先从 `@/config` 导入。

---

## 4. 内容系统

### 4.1 Content Collections

项目使用 `src/content.config.ts` 定义内容集合。

`posts` 集合读取：

```text
src/content/posts/**/*.md
src/content/posts/**/*.mdx
```

文章 frontmatter 应包含：

```yaml
title: 文章标题
published: 发布时间
updated: 更新时间，可选
draft: 是否草稿
description: 摘要
image: 封面图
tags: 标签数组
category: 分类
lang: 文章语言
pinned: 是否置顶
author: 作者
sourceLink: 原文链接
licenseName: 许可协议名称
licenseUrl: 许可协议地址
comment: 是否启用评论
password: 文章密码
passwordHint: 密码提示
```

还应存在内部字段：

```yaml
prevTitle
prevSlug
nextTitle
nextSlug
```

这些字段由排序逻辑注入，用于上一篇/下一篇文章导航。

### 4.2 文章排序

文章读取逻辑放在 `src/utils/content-utils.ts`。

排序规则：

1. 生产环境过滤 `draft: true` 的文章。
2. 开发环境显示草稿，方便本地预览。
3. 置顶文章排在最前。
4. 置顶状态相同时，按发布时间倒序排列。
5. 排序后为每篇文章注入上一篇和下一篇文章信息。

### 4.3 标签与分类

应提供工具函数生成标签列表和分类列表。

标签逻辑：

- 遍历所有文章 tags。
- 统计每个标签出现次数。
- 按字母或本地化规则排序。

分类逻辑：

- 遍历所有文章 category。
- 未分类文章归入国际化文本中的“未分类”。
- 统计每个分类文章数量。
- 生成分类 URL。

### 4.4 相关文章推荐

文章详情页应提供相关推荐。

推荐算法可综合：

- 标签 Jaccard 相似度。
- 标题分词相似度。
- 发布时间新鲜度。
- 同分类加分。

排除当前文章和加密文章。优先推荐标签匹配文章，不足时用时间新鲜度和分类加分补足。

---

## 5. 页面实现

### 5.1 首页与分页列表

首页由 `src/pages/[...page].astro` 实现。

实现逻辑：

1. 调用 `getSortedPosts()` 获取排序后的文章。
2. 使用 Astro `paginate` 生成分页。
3. 分页大小读取 `siteConfig.pagination.postsPerPage`。
4. 将分页数据传给 `PostPage` 组件。
5. 当总页数大于单页大小时显示 `Pagination` 组件。

列表页应支持响应式布局，并能根据配置切换列表、网格、多列、瀑布流等展示方式。

### 5.2 文章详情页

文章详情页由 `src/pages/posts/[...slug].astro` 实现。

实现逻辑：

1. `getStaticPaths()` 读取全部文章。
2. 将文章 id 转换为 slug。
3. 使用 `render(entry)` 渲染文章内容。
4. 获取 `Content`、`headings` 和 `remarkPluginFrontmatter`。
5. 计算文章字数、阅读时间、发布时间、更新时间。
6. 处理文章封面图。
7. 处理分享海报需要的头像和封面图 URL。
8. 生成 JSON-LD 结构化数据。
9. 获取相关文章。
10. 使用 `MainGridLayout` 包裹文章主体。

文章页应包含：

- 标题
- 封面图
- 发布时间
- 更新时间
- 字数
- 阅读时间
- 分类
- 标签
- 加密标识
- Markdown 正文
- 版权协议
- 相关文章
- 分享海报
- 评论区

### 5.3 归档、分类、标签

归档页按时间组织文章。

分类页展示所有分类及数量。

标签页展示所有标签及数量。

这些页面都应读取 Content Collections，而不是手写静态数据。

### 5.4 搜索页

搜索使用 Pagefind。

构建时运行：

```bash
pagefind --site dist
```

页面应加载 Pagefind 生成的索引，实现客户端全文搜索。

### 5.5 相册页

相册配置来自 `galleryConfig.ts`。

相册资源主要放在：

```text
public/gallery/
```

`src/utils/gallery-utils.ts` 负责读取相册目录、图片文件、封面图等信息。

### 5.6 OG 图片

`src/pages/og/[...slug].ts` 负责生成文章 Open Graph 图片。

实现方式：

- 读取文章元数据。
- 读取站点配置和作者资料。
- 使用 Satori 将 HTML/CSS 转成 SVG。
- 使用 Sharp 将 SVG 转成 PNG。
- 输出带缓存头的 PNG 响应。

---

## 6. 全局布局与 SEO

### 6.1 `Layout.astro`

`Layout.astro` 负责完整 HTML 文档。

它应处理：

- `<!doctype html>`
- `<html lang="">`
- 页面标题
- description
- keywords
- author
- favicon
- Open Graph meta
- Twitter Card meta
- viewport
- generator
- 站点语言
- 主题色 CSS 变量
- 背景壁纸模式
- 导航透明模式
- 分析统计脚本
- 全局样式引入
- 功能管理组件引入

页面标题规则：

- 如果页面传入 title，则使用 `页面标题 - 站点标题`。
- 如果没有传入 title，则使用 `站点标题 - 站点副标题`。
- 如果没有副标题，则只使用站点标题。

### 6.2 `MainGridLayout.astro`

`MainGridLayout.astro` 负责站点视觉结构。

它应组合：

- 顶部导航栏
- banner 区域
- 背景遮罩
- 左侧栏
- 右侧栏
- 主内容区
- 浮动目录
- 页面过渡容器

布局应支持桌面、平板、移动端响应式变化。

### 6.3 SEO

每个页面应有合理的 SEO 信息。

文章页应额外设置：

- `og:type = article`
- `og:image`
- JSON-LD BlogPosting
- 发布时间
- 文章语言
- 作者信息

普通页面使用：

- `og:type = website`

---

## 7. Markdown 增强系统

Markdown 渲染管线配置在 `astro.config.mjs` 中。

### 7.1 Remark 插件

应使用：

- `remarkMath`：数学公式。
- `remarkReadingTime`：字数和阅读时间。
- `remarkImageGrid`：图片网格。
- `remarkExcerpt`：文章摘要。
- `remarkDirective`：指令语法。
- `remarkSectionize`：章节结构。
- `remarkMermaid`：Mermaid 代码块。
- `remarkPlantuml`：PlantUML 代码块。
- `remark-admonition-to-blockquote-callout`：兼容 Python Markdown admonition。
- 自定义 `parseDirectiveNode`：把指令节点转成 rehype 可处理结构。

### 7.2 Rehype 插件

应使用：

- `rehypeKatex`：渲染数学公式。
- `rehypeCallouts`：提示块。
- `rehypeSlug`：标题 id。
- `rehypeAutolinkHeadings`：标题锚点。
- `rehypeMermaid`：渲染 Mermaid。
- `rehypePlantuml`：渲染 PlantUML。
- `rehypeFigure`：图片 figure 结构。
- `rehypeExternalLinks`：外链处理。
- `rehypeEmailProtection`：邮箱保护。
- `rehypeComponents`：自定义组件注入。

### 7.3 GitHub 卡片

Markdown 中应支持自定义 GitHub 卡片组件。

通过 `rehypeComponents` 注册：

```text
github -> GithubCardComponent
```

---

## 8. 代码块系统

代码块使用 `astro-expressive-code`。

应支持：

- 明暗主题。
- 行号。
- 复制按钮。
- 语言徽章。
- 折叠代码区块。
- shellsession 默认不显示行号。
- 根据当前主题切换代码块主题。

代码块样式应读取 `expressiveCodeConfig.ts`。

可配置：

- 深色主题
- 浅色主题
- 是否启用语言徽章
- 是否启用折叠
- 折叠阈值
- 预览行数
- 默认是否折叠

---

## 9. 交互功能

### 9.1 Svelte Islands

需要强交互的组件使用 Svelte。

例如：

- 分享海报
- 搜索交互
- 音乐播放器
- 部分动态控件

静态内容仍应尽量使用 Astro 组件预渲染。

### 9.2 页面过渡

使用 `@swup/astro` 实现页面过渡。

需要配置的容器包括：

- `#banner-overlay-container`
- `#banner-dim-container`
- `#swup-container`
- `#left-sidebar-dynamic`
- `#right-sidebar-dynamic`
- `#floating-toc-wrapper`

Swup 应启用：

- cache
- preload
- accessibility
- updateHead
- globalInstance

并应正确处理锚点链接，避免破坏浏览器原生锚点跳转。

### 9.3 图片预览

使用 Fancybox 实现图片预览。

图片预览逻辑由 `FancyboxManager.astro` 或相关 feature 组件管理。

### 9.4 音乐播放器

音乐播放器配置来自 `musicConfig.ts`。

应支持：

- 播放列表。
- 封面图。
- 播放状态。
- 全局挂载。

### 9.5 特效

项目支持樱花等视觉特效。

配置来自 `effectsConfig.ts`。

特效脚本应避免影响主内容性能，并且允许通过配置关闭。

### 9.6 看板娘

项目支持 Live2D/Spine 看板娘。

配置来自 `pioConfig.ts`。

资源位于：

```text
public/pio/
```

应支持模型文件、纹理、动作、音频等资源。

---

## 10. 图片与资源系统

### 10.1 图片来源

项目图片分为两类。

第一类是 `public` 静态资源：

```text
public/assets/images
public/gallery
public/favicon
public/pio
```

这些资源会原样公开访问。

第二类是 `src/assets/images` 源图片：

```text
src/assets/images/DesktopWallpaper
src/assets/images/MobileWallpaper
src/assets/images/avatar.avif
src/assets/images/cover.avif
```

这些图片会被 Astro 构建系统处理和优化。

### 10.2 图片工具

图片相关逻辑放在 `src/utils/image-utils.ts` 和 `src/utils/lqip-utils.ts`。

应支持：

- 本地图片。
- public 图片。
- 远程图片。
- 随机 API 图片。
- 文章封面图处理。
- 头像 URL 处理。
- LQIP 占位色。
- 针对特殊远程图片域名设置 referrerpolicy。

### 10.3 LQIP

构建时运行：

```bash
pnpm lqips
```

实际脚本为：

```bash
npx tsx scripts/generate-lqips.ts
```

脚本扫描 `src` 和 `public` 中的图片，忽略 favicon、pio、effects、music 等目录，生成：

```text
src/constants/lqips.json
```

LQIP 数据用于图片加载前的低质量占位色。

---

## 11. 样式系统

### 11.1 全局样式

全局样式入口包括：

- `src/styles/main.css`
- `src/styles/variables.styl`
- `src/styles/markdown-extend.styl`
- `@rehype-callouts-theme`

### 11.2 主题系统

主题色由 `siteConfig.themeColor.hue` 控制。

应支持：

- 亮色主题。
- 暗色主题。
- 跟随系统主题。
- 360 度色相调节。

主题状态通过 HTML attribute 或 CSS 变量驱动。

### 11.3 壁纸系统

背景壁纸配置来自 `backgroundWallpaper.ts`。

应支持：

- banner 模式。
- 全屏壁纸模式。
- 透明壁纸模式。
- 纯色背景模式。
- 桌面端壁纸。
- 移动端壁纸。
- 导航栏透明模式。

### 11.4 响应式设计

站点必须适配：

- 桌面端。
- 平板端。
- 移动端。

布局应在不同屏幕宽度下自动调整侧栏、文章列表和导航。

---

## 12. 国际化

项目应支持 UI 多语言。

语言文本位于：

```text
src/i18n/
```

核心形式：

- `I18nKey` 定义翻译键。
- `translation` 根据当前语言返回文本。

站点默认语言来自 `siteConfig.lang`。

文章也可通过 frontmatter 的 `lang` 指定语言。

---

## 13. 评论、统计与外部集成

### 13.1 评论系统

评论配置来自 `commentConfig.ts`。

文章 frontmatter 中的 `comment` 可控制单篇文章是否启用评论。

### 13.2 统计分析

统计配置来自 `analyticsConfig.ts`。

应支持：

- Google Analytics。
- Microsoft Clarity。
- Umami。
- 51.LA。

统计脚本由 `Layout.astro` 按配置注入。

### 13.3 外部链接与安全

外链应通过 rehype 插件统一处理。

邮箱应通过 `rehypeEmailProtection` 保护。

远程图片需要针对部分域名设置 `referrerpolicy`，避免 403。

---

## 14. 构建流程

### 14.1 开发命令

```bash
pnpm dev
pnpm start
```

启动 Astro 开发服务器，默认地址为：

```text
http://localhost:14321
```

### 14.2 检查命令

```bash
pnpm check
pnpm type-check
```

`pnpm check` 运行 Astro 诊断。

`pnpm type-check` 运行：

```bash
tsc --noEmit --isolatedDeclarations
```

### 14.3 格式化与 lint

```bash
pnpm format
pnpm lint
```

使用 Biome 处理 `src` 目录。

### 14.4 构建命令

```bash
pnpm build
```

构建流程应依次执行：

1. `node scripts/generate-icons.js`
2. `npx tsx scripts/generate-lqips.ts`
3. `astro build`
4. `npx tsx scripts/subset-fonts.ts`
5. `pagefind --site dist`

### 14.5 预览命令

```bash
pnpm preview
```

用于预览生产构建结果。

### 14.6 新文章命令

```bash
pnpm new-post
```

用于脚手架生成新文章。

---

## 15. 代码风格

项目使用 Biome。

要求：

- 使用 tab 缩进。
- JavaScript/TypeScript 字符串使用双引号。
- Astro/Svelte 组件使用 PascalCase。
- 配置模块使用 camelCase，并以 `Config.ts` 结尾。
- 工具函数文件使用描述性 kebab-case 或项目已有命名方式。
- 避免无关格式化。
- 保持 `src/types` 与 `src/config` 同步。

---

## 16. 复刻实现要求

如果要从零复刻 Firefly，应遵循以下原则。

### 16.1 高配置化

不要把以下内容硬编码在组件中：

- 站点名称。
- 作者头像。
- 作者简介。
- 导航项。
- 侧栏组件。
- 评论系统。
- 统计脚本。
- 音乐列表。
- 壁纸。
- 相册。
- 赞助方式。
- 页脚 HTML。
- 许可证。
- 主题色。
- 页面开关。

这些都应来自 `src/config`。

### 16.2 静态优先

能静态生成的页面都应静态生成。

不要把博客主体做成纯客户端渲染。

### 16.3 组件边界清晰

布局组件只负责结构。

页面组件负责页面级组合。

功能组件负责单一功能。

工具函数负责数据处理。

配置文件负责可变内容。

### 16.4 内容与展示分离

Markdown/MDX 只负责内容。

文章排序、分类、标签、推荐等逻辑放在工具函数。

页面只组合数据和组件。

### 16.5 性能与 SEO

必须关注：

- 首屏渲染。
- 静态 HTML 输出。
- 图片优化。
- 搜索索引。
- sitemap。
- RSS。
- OG 图片。
- 结构化数据。

---

## 17. 一句话总结

Firefly 的实现思路是：用 Astro 构建静态优先的博客骨架，用 Content Collections 管理文章，用配置系统驱动站点外观和功能，用 Markdown 插件增强写作能力，用 Svelte islands 承担必要交互，用 Pagefind、Satori、Sharp、Expressive Code、Swup 等工具补齐搜索、OG 图片、代码块和页面过渡体验，最终形成一个高度可配置、视觉丰富、性能友好的现代个人博客主题。

