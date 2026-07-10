# Steve 项目复现长提示词

下面这段提示词用于让 AI 从零复现当前项目 `http://127.0.0.1:24321/` 的效果与工程结构。请把它作为完整需求交给代码生成模型使用。

---

请构建一个名为 **Steve** 的现代化个人博客主题项目，要求能复现当前站点的工程架构、页面布局、视觉风格、内容系统、交互能力与构建流程。

项目不是单页营销站，也不是纯客户端 SPA，而是一个 **Astro 7 静态优先博客主题**。它应以 Markdown/MDX 内容为核心，以配置文件驱动站点外观和功能，以 Svelte islands 承担必要交互，最终呈现一个清新、柔和、可高度自定义的个人博客系统。

---

## 1. 技术栈与基础要求

- 使用 `Astro 7.0.2` 作为静态站点生成框架。
- 使用 `TypeScript 5.9+`。
- 使用 `Svelte 5` 实现搜索、设置面板、音乐播放器、交互控件、相册/番剧等动态区域。
- 使用 `Tailwind CSS 4` 风格的原子类和 CSS 变量系统。
- 使用 `pnpm` 作为包管理器，并通过 `preinstall` 限制只允许 pnpm。
- 使用 `Biome` 作为格式化和 lint 工具，要求 tab 缩进、JS/TS 双引号。
- 使用 `Astro Content Collections` 管理 Markdown/MDX 文章。
- 使用 `Pagefind` 生成全文搜索索引。
- 使用 `astro-expressive-code` 渲染代码块。
- 使用 `Satori` + `Sharp` 生成 Open Graph 图片。
- 使用 `@swup/astro` 实现页面过渡。
- 使用 `astro-icon` / Iconify 图标集，包括 `material-symbols`、`fa7-brands`、`fa7-solid`、`simple-icons` 等。

核心依赖应包含：

```json
{
	"astro": "7.0.2",
	"@astrojs/svelte": "9.0.0",
	"svelte": "^5.56.3",
	"typescript": "^5.9.2",
	"pagefind": "^1.5.2",
	"sharp": "^0.35.2",
	"satori": "^0.26.0",
	"astro-expressive-code": "^0.43.1",
	"@swup/astro": "^1.8.0",
	"@biomejs/biome": "2.5.0"
}
```

---

## 2. 项目目录结构

按以下结构组织项目，不要把配置、组件、工具函数和内容混在一起：

```text
src/
  assets/       构建期可优化图片资源，例如头像、封面、桌面壁纸、移动壁纸
  components/   可复用 UI、布局、控件、功能组件
  config/       站点配置文件，所有可变内容从这里读取
  constants/    常量与构建生成文件，如 icons.ts、lqips.json
  content/      Markdown/MDX 内容
  i18n/         多语言文本与翻译键
  layouts/      Layout.astro 与 MainGridLayout.astro
  pages/        Astro 文件路由
  plugins/      remark/rehype 插件
  styles/       全局样式、Markdown 样式、导航栏、壁纸、响应式样式
  types/        config 类型定义
  utils/        内容、图片、URL、布局、响应式、日期等工具函数
public/
  assets/       静态图片、音乐、CSS、JS
  favicon/      favicon
  gallery/      相册资源
  pio/          Live2D/Spine 看板娘资源
docs/
scripts/
```

组件目录分组：

```text
src/components/
  analytics/  GoogleAnalytics、MicrosoftClarity、Umami、51.LA
  comment/    Giscus、Waline、Twikoo、Artalk、Disqus
  common/     Button、Dropdown、ImageWrapper、Markdown、Pagination
  controls/   搜索、主题切换、显示设置、返回顶部、浮动控件
  features/   音乐、背景视频、樱花特效、Live2D/Spine、Fancybox、加密文章
  layout/     Navbar、Footer、SideBar、PostCard、PostMeta、CategoryBar
  misc/       分享海报、许可协议、相关文章
  pages/      gallery、anime、bangumi、search 等页面级组件
  widget/     Profile、Announcement、Music、Categories、Tags、Calendar、Stats、TOC
```

---

## 3. 全局视觉风格

整体视觉应复现当前 Steve/Steve 站点：

- 站点标题为 `Steve`，副标题为 `Demo site`。
- HTML `lang` 使用 `zh-CN`。
- 默认主题模式为 `system`，主题色 hue 为 `165`，整体呈现薄荷绿/青绿色点缀。
- 字体使用系统 sans-serif 栈：`system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif`。
- 页面背景不是纯白，而是由 `--page-bg` 控制的浅色背景，视觉上接近很淡的青绿色。
- 卡片使用圆角、浅阴影、浅边框和白色/半透明底色。
- 所有主内容、侧栏、小组件都使用统一的 `card-base` 风格。
- 主按钮、标签、图标和强调线使用主题色 `--primary`。
- 卡片内标题粗体、字号较大，正文说明使用较低透明度的黑色。
- 移动端保持卡片紧凑，桌面端使用多列网格。

全局 CSS 变量需要支持：

```css
--hue: 165;
--page-bg;
--primary;
--radius-large;
--card-bg;
--btn-regular-bg;
--btn-content;
--banner-height;
--banner-height-extend;
--page-width;
--card-transparent-opacity;
```

---

## 4. 首页第一屏与壁纸系统

首页第一屏必须是大型背景壁纸 banner：

- 默认壁纸模式为 `"banner"`。
- 支持四种壁纸模式：
  - `banner`：横幅壁纸。
  - `fullscreen`：全屏壁纸。
  - `overlay`：全屏透明壁纸，卡片半透明覆盖其上。
  - `none`：纯色背景。
- `switchable: true`，用户可在显示设置面板切换模式。
- 桌面端使用 `src/assets/images/DesktopWallpaper/d1.avif` 到 `d6.avif`。
- 移动端使用 `src/assets/images/MobileWallpaper/m1.avif` 到 `m6.avif`。
- 图片用 Astro 图片优化系统处理，桌面宽度可生成 `1280/1920/2560`，移动端生成 `640/750/1080`。
- banner 图片 `object-position` 为 `"0% 20%"`。
- banner 有黑色遮罩，默认 `dimOpacity: 0.2`。
- banner 底部支持水波纹 SVG 过渡，四层 wave 透明度分别约 `0.25/0.5/0.65/0.75`，填充 `--page-bg`。
- 当水波纹关闭时，用底部线性渐变从透明过渡到 `--page-bg`，高度约 `10%`。

首页文字叠在 banner 上：

- 居中显示，容器为 `absolute inset-0 flex items-center justify-center`。
- 主标题：`Lovely firefly!`
- 主标题白色、粗体、大字号，默认 `3.8rem`。
- 副标题白色 90% 透明度，默认 `1.5rem`。
- 副标题为打字机循环文本：

```text
In Reddened Chrysalis, I Once Rest
From Shattered Sky, I Free Fall
Amidst Silenced Stars, I Deep Sleep
Upon Lighted Fyrefly, I Soon Gaze
From Undreamt Night, I Thence Shine
In Finalized Morrow, I Full Bloom
```

打字机参数：

- `speed: 100`
- `deleteSpeed: 50`
- `pauseTime: 2000`

背景视频：

- 支持 `playerEnable: true`。
- `playerUrl` 可为数组，播放模式为 `"random"`。
- 导航栏显示播放/暂停按钮，点击后通过自定义事件控制背景视频。

---

## 5. 顶部导航栏

导航栏必须复现当前站点的半透明磨砂玻璃效果：

- 外层固定在顶部，`stickyNavbar: true`。
- 高度约 `4.5rem`，类名行为接近 `h-18`。
- 桌面端宽度约 `92vw`，最大宽度受 `--page-width` 控制。
- 背景为半透明白色，开启 `backdrop-filter: blur(5px)`。
- 大圆角、轻微阴影，滚动后增加 sticky shadow。
- 首屏位于 banner 上方，视觉上像悬浮在壁纸上的磨砂导航条。
- 左侧 logo 为 `assets/images/firefly.png`，尺寸约 `28px`，旁边显示 `Steve`。
- 导航标题加粗，颜色黑色，暗色模式时白色。
- 中间菜单桌面端居中排列，移动端隐藏为汉堡菜单。
- 右侧包含搜索、背景视频播放、显示设置、亮暗色切换、移动端菜单按钮。

导航项：

```text
主页 -> /
文章 -> 子菜单：归档 /archive/，分类 /categories/，标签 /tags/
友链 -> /friends/
留言 -> /guestbook/
我的 -> 子菜单：相册 /gallery/，追番 /anime/，番组计划 /bangumi/
关于 -> 子菜单：打赏 /sponsor/，关于我 /about/
链接 -> 子菜单：GitHub、Gitee、QQ交流群、Steve文档
```

导航项需要从 `src/config/navBarConfig.ts` 读取，并根据 `siteConfig.pages` 自动过滤被关闭的页面。若某个父菜单过滤后只剩一个子项，应自动提升为普通链接。

---

## 6. 主布局系统

创建两个核心布局：

### `Layout.astro`

负责完整 HTML 文档：

- `<!doctype html>`
- `<html lang={siteLang}>`
- `<title>` 规则：
  - 有页面标题时：`页面标题 - Steve`
  - 无页面标题时：`Steve - Demo site`
- `description` 默认使用 `siteConfig.description`。
- `keywords` 来自 `siteConfig.keywords`。
- `author` 来自 `profileConfig.name`。
- Open Graph：
  - `og:site_name`
  - `og:url`
  - `og:title`
  - `og:description`
  - `og:type`，文章页为 `article`，普通页为 `website`
  - 文章开启 OG 图片时输出 `/og/[slug].png`
- Twitter Card 使用 `summary_large_image`。
- favicon 来自配置，默认 `/favicon/favicon.ico`。
- 初始化主题，避免页面闪烁。
- 初始化 `--hue`、`data-theme`、壁纸模式、卡片透明度、banner 高度。
- 注入 Google Analytics、Microsoft Clarity、Umami、51.LA。
- 注入 Fancybox、MusicManager、SakuraEffect、ConfigCarrier 等全局功能。
- 处理特殊远程图片域名 `*.hdslb.com`、`*.bilibili.com`，自动设置 `referrerpolicy="no-referrer"`。

### `MainGridLayout.astro`

负责页面视觉骨架：

- 渲染 Navbar。
- 渲染壁纸 wrapper。
- 渲染 banner 遮罩、首页文字、页面标题、文章页 banner meta。
- 渲染水波纹或渐变过渡。
- 渲染主内容网格。
- 渲染左右侧栏。
- 渲染移动端底部侧栏组件。
- 渲染 Footer。
- 渲染 SpineModel / Live2DWidget。
- 渲染 FloatingControls。

主网格规则：

- 使用 CSS grid。
- 桌面宽屏三列：`17.5rem 1fr 17.5rem`。
- 平板双列：根据 `tabletSidebar` 显示左侧栏或右侧栏。
- 移动端单列，并可把部分侧栏组件放到底部。
- `pageWidth: 100rem`。
- 网格间距 `gap-4`。
- 内容与侧栏卡片都使用统一卡片风格。

---

## 7. 侧边栏系统

侧边栏必须完全配置化，由 `src/config/sidebarConfig.ts` 驱动。

默认配置：

```ts
enable: true
position: "both"
tabletSidebar: "left"
hideSidebarOnPostPage: false
showBothSidebarsOnPostPage: true
```

左侧栏组件顺序：

```text
profile       top     文章页显示
announcement top     文章页显示
music         sticky  文章页显示
categories    sticky  文章页显示，超过 5 个折叠
tags          sticky  文章页显示，超过 10 个折叠
```

右侧栏组件顺序：

```text
stats       top     文章页显示
siteInfo    top     文章页显示
calendar    sticky  非文章页显示，显示年度文章热力图
sidebarToc  sticky  仅文章页显示
advertisement 可选，默认关闭
```

移动端底部组件：

```text
profile
announcement
music
categories
tags
stats
siteInfo
```

侧栏视觉：

- 每个组件为圆角卡片。
- 标题左侧有主题色竖条。
- sticky 组件在滚动时保持顶部间距。
- profile 卡片显示头像、名字 `Steve`、签名 `Hello, I'm Steve.`、社交图标。
- stats 卡片显示文章数、分类数、标签数、总字数、运行时长、最后活动。
- music 卡片显示歌曲封面、标题、作者、进度条、播放按钮、上一首/下一首。
- categories 和 tags 使用胶囊按钮样式，数量徽章使用主题色。

---

## 8. 首页文章列表

首页路由使用 `src/pages/[...page].astro`：

- 读取 `getSortedPosts()`。
- 使用 Astro `paginate` 生成分页。
- 每页文章数为 `siteConfig.pagination.postsPerPage`，默认 `10`。
- 主体交给 `PostPage` 或同等组件渲染。
- 文章列表上方显示分类快捷导航 `CategoryBar`。
- 分类导航条包含 `归档`、分类名和“更多”等入口。

文章列表布局配置：

```ts
postListLayout: {
	defaultMode: "list",
	mobileDefaultMode: "grid",
	allowSwitch: true,
	descriptionLines: 2,
	showStatsIcons: true,
	tagsPosition: "bottom",
	grid: {
		masonry: false,
		columnWidth: 320
	}
}
```

文章卡片结构：

- 外层类名接近：

```text
post-card-wrapper card-base flex flex-col-reverse w-full rounded-(--radius-large) overflow-hidden relative
```

- 无封面时右侧显示进入按钮，按钮为窄竖条，内部是 `material-symbols:chevron-right-rounded`。
- 标题大号粗体，hover 变为主题色。
- 标题左侧在桌面端显示主题色竖条。
- 置顶文章显示“置顶”徽章。
- 加密文章标题旁显示锁图标。
- 元数据包括发布日期、分类、标签、字数、阅读时间，根据配置决定显示。
- 摘要最多显示 2 行，使用 line clamp。
- 底部标签以圆角胶囊呈现，背景 `black/5` 或暗色 `white/10`。

首页示例文章必须包含至少这些内容：

```text
安装必备环境
Steve 一款清新美观的 Astro 博客主题模板
Steve 简单使用指南
Steve 代码块示例
草稿示例
Steve 文章加密
Steve 布局系统详解
KaTeX 数学公式示例
Markdown 扩展功能
Markdown Mermaid 图表
Markdown PlantUML 图表
MDX 组件示例
视频文章
```

分类示例：

```text
文章示例
博客指南
```

标签示例：

```text
Steve
Markdown
博客
指南
环境配置
Node.js
pnpm
Git
KaTeX
Math
Mermaid
PlantUML
MDX
视频
密码保护
```

---

## 9. 内容系统与文章排序

使用 `src/content.config.ts` 定义集合：

```ts
const postsCollection = defineCollection({
	loader: glob({ pattern: "**/*.{md,mdx}", base: "./src/content/posts" }),
	schema: z.object({
		title: z.string(),
		published: z.date(),
		updated: z.date().optional(),
		draft: z.boolean().optional().default(false),
		description: z.string().optional().default(""),
		image: z.string().optional().default(""),
		tags: z.array(z.string()).optional().default([]),
		category: z.string().optional().nullable().default(""),
		lang: z.string().optional().default(""),
		pinned: z.boolean().optional().default(false),
		author: z.string().optional().default(""),
		sourceLink: z.string().optional().default(""),
		licenseName: z.string().optional().default(""),
		licenseUrl: z.string().optional().default(""),
		comment: z.boolean().optional().default(true),
		password: z.string().optional().default(""),
		passwordHint: z.string().optional().default(""),
		prevTitle: z.string().default(""),
		prevSlug: z.string().default(""),
		nextTitle: z.string().default(""),
		nextSlug: z.string().default("")
	})
})
```

排序规则：

1. 生产环境过滤 `draft: true`。
2. 开发环境保留草稿。
3. `pinned: true` 排在最前。
4. 同置顶状态按 `published` 倒序。
5. 排序后注入上一篇/下一篇信息。

工具函数应提供：

- `getSortedPosts()`
- `getAllCategories()`
- `getAllTags()`
- `getRelatedPosts()`
- slug 与 URL 转换函数。

---

## 10. 页面路由

必须实现以下 Astro 路由：

```text
/                         src/pages/[...page].astro
/posts/[...slug]/         src/pages/posts/[...slug].astro
/archive/                 src/pages/archive.astro
/categories/              src/pages/categories/index.astro
/tags/                    src/pages/tags/index.astro
/search/                  src/pages/search.astro
/friends/                 src/pages/friends.astro
/guestbook/               src/pages/guestbook.astro
/about/                   src/pages/about.astro
/sponsor/                 src/pages/sponsor.astro
/gallery/                 src/pages/gallery/index.astro
/gallery/[album]/         src/pages/gallery/[album].astro
/bangumi/                 src/pages/bangumi.astro
/anime/                   src/pages/anime.astro
/rss/                     src/pages/rss.astro
/rss.xml                  src/pages/rss.xml.ts
/robots.txt               src/pages/robots.txt.ts
/og/[...slug].png         src/pages/og/[...slug].ts
/api/allPostMeta.json     src/pages/api/allPostMeta.json.ts
/404                      src/pages/404.astro
```

所有普通页面使用 `MainGridLayout` 包裹。文章页传入 `headings`、`bannerPostMeta`、`postSlug`、`postCategory`。

文章页必须包含：

- banner 中的文章标题与 meta。
- 文章标题、封面、发布时间、更新时间、字数、阅读时间、分类、标签。
- Markdown 正文。
- 文章加密功能。
- 文章过期提示/上次编辑时间。
- 版权许可协议。
- 相关文章推荐。
- 分享海报。
- 评论区。
- 右侧目录或浮动目录。

---

## 11. Markdown 与 MDX 增强

Astro Markdown 管线需支持：

Remark 插件：

```text
remarkMath
remarkReadingTime
remarkImageGrid
remarkExcerpt
remarkDirective
remarkSectionize
remarkMermaid
remarkPlantuml
remark-admonition-to-blockquote-callout
自定义 parseDirectiveNode
```

Rehype 插件：

```text
rehypeKatex
rehypeCallouts
rehypeSlug
rehypeAutolinkHeadings
rehypeMermaid
rehypePlantuml
rehypeFigure
rehypeExternalLinks
rehypeEmailProtection
rehypeComponents
```

需要支持的 Markdown 能力：

- GitHub 风格 Markdown。
- 数学公式 KaTeX。
- Mermaid 图表。
- PlantUML 图表。
- GitHub/Obsidian/VitePress/Docusaurus 风格提示块。
- GitHub 仓库卡片组件，语法映射到 `GithubCardComponent`。
- 图片自动 figure 包装。
- 图片宫格。
- 外链自动处理。
- 邮箱保护。
- 代码块标题、行号、高亮、折叠、复制按钮。

代码块使用 Expressive Code：

- 支持明暗主题切换。
- 支持语言徽章。
- 支持折叠区块。
- `shellsession` 默认不显示行号。
- 根据 `data-theme` 自动切换主题。

---

## 12. 搜索系统

搜索入口位于导航栏右侧。

- 生产构建后使用 Pagefind。
- 构建命令末尾运行 `pagefind --site dist`。
- 搜索面板使用 Svelte island。
- 输入关键词后展示结果标题、摘要、路径。
- 若 Pagefind 脚本加载失败，提供空结果 fallback，不能让页面崩溃。

---

## 13. 显示设置面板

导航栏的调色盘按钮打开显示设置面板。

面板功能：

- 主题色 hue 调节，范围 0-360。
- 亮色、暗色、跟随系统模式切换。
- 壁纸模式切换：banner/fullscreen/overlay/none。
- 文章列表布局切换：list/grid。
- overlay 模式下调节壁纸透明度、模糊度、卡片透明度。
- 控制首页 banner 标题是否显示。
- 控制水波纹、渐变、壁纸轮播等可选效果。

设置需要写入 `localStorage`，并在页面加载前尽可能早地应用，避免闪烁。

---

## 14. 交互与页面过渡

使用 `@swup/astro` 实现切页过渡。

Swup 容器：

```text
#banner-overlay-container
#banner-dim-container
#swup-container
#left-sidebar-dynamic
#right-sidebar-dynamic
#floating-toc-wrapper
```

Swup 行为：

- 启用 cache、preload、accessibility、updateHead、globalInstance。
- 切页开始时显示顶部进度条。
- 切页时动态调整 banner 高度、壁纸显示、主网格列数、侧栏可见性。
- 切页完成后重新初始化图标、主题、目录、评论、图片 fade-in。
- 正确处理锚点链接，不破坏浏览器原生锚点跳转。

滚动行为：

- 滚动超过 banner 高度后显示返回顶部按钮。
- sticky 导航栏保持显示。
- banner 模式下目录在 banner 区域隐藏，进入正文后显示。
- 导航栏滚动后添加阴影。

---

## 15. 媒体与资源

图片来源分两类：

```text
src/assets/images/
  avatar.avif
  cover.avif
  profile-avatar.jpg
  firefly.png
  DesktopWallpaper/d1.avif ... d6.avif
  MobileWallpaper/m1.avif ... m6.avif

public/
  assets/images/
  assets/music/
  gallery/
  favicon/
  pio/
```

图片工具需要支持：

- src 本地图像优化。
- public 静态图。
- 远程图片。
- 随机 API 图片。
- 文章封面图处理。
- 头像 URL 处理。
- LQIP 占位。
- 针对 Bilibili 等防盗链域名添加 no-referrer。

LQIP 构建：

- 脚本扫描 `src` 和 `public` 中的图片。
- 忽略 favicon、pio、effects、music 等目录。
- 生成 `src/constants/lqips.json`。
- 图片加载前显示低质量渐变占位，加载后淡出。

---

## 16. 音乐、特效与看板娘

音乐播放器：

- 支持导航栏弹出播放器。
- 支持侧栏播放器卡片。
- 显示封面、歌曲名、作者、进度、音量、播放/暂停、上一首/下一首、播放列表。
- 当前示例音乐为 `使一颗心免于哀伤`，歌手显示 `知更鸟 / HOYO-MiX / Chevy`。

特效：

- 支持樱花特效，由 `effectsConfig.ts` 控制。
- 特效应可关闭，不影响主要内容性能。

看板娘：

- 支持 Spine 和 Live2D。
- 资源位于 `public/pio/`。
- Spine 示例资源为 `public/pio/models/spine/firefly/`。
- Live2D 示例资源为 `public/pio/models/live2d/snow_miku/`。
- 看板娘由配置控制是否启用。

---

## 17. 国际化

实现 UI 多语言系统：

```text
src/i18n/i18nKey.ts
src/i18n/translation.ts
src/i18n/languages/zh_CN.ts
src/i18n/languages/zh_TW.ts
src/i18n/languages/en.ts
src/i18n/languages/ja.ts
src/i18n/languages/ru.ts
```

- 默认语言为 `zh_CN`。
- 页面 HTML lang 转换为 `zh-CN`。
- 文章可通过 frontmatter 的 `lang` 单独指定语言。
- 所有 UI 文本，如“归档”“分类”“标签”“发布时间”“阅读时间”等通过 i18n 获取。

---

## 18. 评论、统计与外部集成

评论系统配置化：

- 支持 Giscus。
- 支持 Waline。
- 支持 Twikoo。
- 支持 Artalk。
- 支持 Disqus。
- 单篇文章可通过 frontmatter `comment: false` 关闭。

统计分析配置化：

- Google Analytics。
- Microsoft Clarity。
- Umami。
- 51.LA。

外部链接：

- 外链自动添加安全属性。
- 邮箱链接进行保护。
- 社交链接来自 `profileConfig.links`。

默认 profile：

```ts
name: "Steve"
bio: "Hello, I'm Steve."
avatar: "assets/images/profile-avatar.jpg"
links:
  qq -> fa7-brands:qq
  GitHub -> fa7-brands:github
  Email -> fa7-solid:envelope
  RSS -> fa7-solid:rss
```

---

## 19. 构建与脚本

`package.json` 脚本必须包含：

```json
{
	"dev": "astro dev",
	"start": "astro dev",
	"check": "astro check",
	"build": "node scripts/generate-icons.js && npx tsx scripts/generate-lqips.ts && astro build && npx tsx scripts/subset-fonts.ts && pagefind --site dist",
	"preview": "astro preview",
	"astro": "astro",
	"type-check": "tsc --noEmit --isolatedDeclarations",
	"new-post": "node scripts/new-post.js",
	"format": "biome format --write ./src",
	"lint": "biome check --write ./src",
	"preinstall": "npx only-allow pnpm",
	"icons": "node scripts/generate-icons.js",
	"lqips": "npx tsx scripts/generate-lqips.ts"
}
```

构建流程：

1. 生成 Iconify 图标常量。
2. 生成 LQIP 占位数据。
3. Astro 构建静态站点。
4. 字体子集化。
5. Pagefind 生成搜索索引。

开发命令：

```bash
pnpm install
pnpm dev
pnpm check
pnpm type-check
pnpm build
pnpm preview
pnpm new-post
```

---

## 20. 响应式要求

桌面端：

- 顶部导航完整显示。
- 主网格可为双侧栏三列。
- banner 高度接近一屏，首页文字居中。
- 内容区域与 banner 有重叠/过渡关系。

平板端：

- 双侧栏退化为单侧栏。
- 根据 `tabletSidebar` 显示左侧栏。
- 主内容保持卡片式布局。

移动端：

- 顶部导航只显示 logo、搜索、设置、主题切换、菜单按钮。
- 菜单通过浮层面板打开。
- 非首页可隐藏 banner，主内容从导航栏下方开始。
- 侧栏组件移动到页面底部。
- 文章列表默认 grid 模式。
- 所有卡片间距更紧凑，标题不溢出。

---

## 21. 复现验收标准

完成后应满足：

- 打开首页时看到大幅深色森林/Steve 风格壁纸 banner。
- 顶部有半透明磨砂导航栏，左侧是叶子 logo 和 `Steve`。
- 首页 banner 中央/偏右区域显示 `Lovely firefly!` 与循环打字机副标题。
- 向下滚动后进入浅绿色页面背景上的卡片网格。
- 左侧栏显示 profile、公告、音乐、分类、标签。
- 右侧栏显示站点统计、站点信息、日历或文章目录。
- 中间内容显示文章卡片，标题、置顶、日期、分类、摘要、标签样式与当前站点一致。
- 文章页能渲染 Markdown、代码块、KaTeX、Mermaid、PlantUML、提示块、图片预览、评论、相关文章。
- 搜索能在生产构建后使用 Pagefind。
- 显示设置能切换主题色、明暗模式、壁纸模式、文章列表布局。
- `pnpm check`、`pnpm type-check`、`pnpm build` 可通过。

---

## 22. 实现原则

- 配置优先：站点名称、导航、侧栏、壁纸、评论、统计、音乐、相册、赞助、主题色都必须来自 `src/config`。
- 静态优先：文章、归档、分类、标签、普通页面尽量静态生成。
- 岛屿交互：只有搜索、设置、音乐、海报、动态列表等需要交互的部分使用 Svelte。
- 组件边界清晰：布局负责结构，页面负责组合，组件负责展示，工具函数负责数据处理。
- 不要把博客做成纯客户端应用。
- 不要把示例文章、导航项、主题参数硬编码在组件内部。
- 保持视觉轻盈、柔和、清新，避免厚重暗色后台风格。

一句话总结：复现 Steve 时，要用 Astro 搭出静态优先的博客骨架，用配置系统驱动视觉和功能，用 Markdown 插件增强写作能力，用 Svelte islands 补足必要交互，用壁纸 banner、磨砂导航、双侧栏卡片、主题色系统、Pagefind 搜索、Expressive Code、Swup 过渡和 Live2D/音乐等功能共同形成当前这个清新可配置的现代个人博客主题。
