---
title: "新增一篇 Markdown 文章"
published: 2026-07-12T19:00:00+08:00
updated: 2026-07-12T19:00:00+08:00
pinned: false
description: "在 Firefly 的文章目录中创建 Markdown 文件，填写 frontmatter 和正文，并通过本地页面、构建和 Git 差异完成验证。"
tags: [AI编程, Firefly, Markdown, Astro, 内容创作]
category: 博客指南
draft: false
---

完成 [认识 Firefly 文件结构并修改配置](/posts/setup-project-foundation/) 后，已经知道文章放在 `src/content/posts/`。本篇把“写一篇文章”拆成一条可以重复执行的流程：确定文件名、生成 Markdown 文件、填写元数据、写正文、在本地查看，并确认构建没有问题。

## 问题是什么

“我要写一篇文章”还不够具体。本篇要解决的问题是：**如何在 Firefly 中新增一篇可访问、可构建、可继续修改的 Markdown 文章。**

完成后应该得到：

- 一个放在 `src/content/posts/` 下的 `.md` 文件；
- 一组符合 Firefly 内容规则的 frontmatter；
- 一篇能够在本地文章列表和文章详情页中显示的正文；
- 一条可以检查、构建和提交的 Git 改动。

本篇不处理文章配图设计、不修改页面布局，也不同时新增多个文件。先让一篇最小文章完整跑通，再逐步增加图片、代码和图表。

## 适用环境是什么

本文适用于已经完成以下准备的环境：

- 已经 Fork、克隆并进入 Firefly 项目根目录；
- 已经执行过 `pnpm install`；
- Node.js 版本不低于 22，pnpm 可以正常运行；
- 已经用 VS Code 打开项目；
- 本地开发服务器可以通过 `pnpm dev` 启动。

文章内容由 Astro Content Collection 读取，集合配置位于 `src/content.config.ts`。当前文章文件使用 `.md` 或 `.mdx` 扩展名，放错目录、frontmatter 缺字段或类型不正确，都可能导致检查失败。

## 具体怎么做

### 1. 先确定文章主题和 slug

先把文章主题写成一个明确的问题，例如：

> 如何在 Firefly 中新增一篇 Markdown 文章，并确认它能在本地页面访问？

再为文章确定文件名。文件名会参与生成文章网址，建议只使用小写英文字母、数字和短横线：

```text
my-first-firefly-article.md
```

这个文件最终对应：

```text
http://127.0.0.1:24321/posts/my-first-firefly-article/
```

文件名尽量稳定。文章发布后再改 slug，会影响旧链接和搜索结果。

### 2. 使用项目脚本生成文章文件

在项目根目录打开终端，执行：

```bash
pnpm new-post -- my-first-firefly-article
```

这个命令会在 `src/content/posts/` 下生成 `my-first-firefly-article.md`，并写入一份基础 frontmatter。脚本也支持直接传入 `.md` 扩展名：

```bash
pnpm new-post -- my-first-firefly-article.md
```

如果文件已经存在，脚本会停止并提示错误。此时不要覆盖旧文章，先换一个 slug，或者确认自己是否应该编辑已有文件。

### 3. 填写 frontmatter

打开新文件，将顶部内容整理成下面的形式：

```yaml
---
title: "我的第一篇 Firefly 文章"
published: 2026-07-12T19:00:00+08:00
updated: 2026-07-12T19:00:00+08:00
pinned: false
description: "记录我第一次在 Firefly 中新增 Markdown 文章的过程。"
tags: [AI编程, Firefly, Markdown]
category: 博客指南
draft: false
---
```

这些字段的作用是：

| 字段 | 作用 |
| --- | --- |
| `title` | 文章标题，必须是字符串 |
| `published` | 发布时间，决定文章日期和排序 |
| `updated` | 最近修改时间，可选但建议填写 |
| `pinned` | 是否置顶，通常填写 `false` |
| `description` | 首页和搜索结果中使用的摘要 |
| `tags` | 标签数组，多个标签用逗号分隔 |
| `category` | 文章分类 |
| `draft` | 是否草稿，确认发布时填写 `false` |

日期使用完整日期时间时，带上时区，例如 `+08:00`。标签必须是数组，布尔值必须写成 `true` 或 `false`，不要把它们写成带引号的普通文本。

### 4. 写一份最小可用正文

先不要追求很长。可以从一个标题、一个说明段落和一组检查项开始：

```markdown
# 我的第一篇 Firefly 文章

这篇文章用于验证 Markdown 内容能够被 Firefly 读取并显示。

## 我完成了什么

- 创建了 Markdown 文件
- 填写了 frontmatter
- 在本地打开了文章页面
```

文章文件中的第一个 `#` 通常会被作为正文标题，文章页面本身已经有文章主标题。为了避免标题重复，也可以直接从段落或 `##` 小节开始。写作时优先保证内容清楚，再考虑图片、代码块、表格和 Mermaid 图表。

### 5. 需要插入图片时再添加资源

图片可以放在文章相邻的图片目录中，例如：

```text
src/content/posts/
├── images/
│   └── my-first-firefly-article.png
└── my-first-firefly-article.md
```

在 Markdown 中引用：

```markdown
![文章验证截图](./images/my-first-firefly-article.png)
```

图片文件名同样建议使用小写英文和短横线。先让无图片版本通过检查，再一次添加一张图片并重新验证，出错时更容易定位。

### 6. 在本地页面查看文章

启动开发服务器：

```bash
pnpm dev
```

打开终端显示的 `Local` 地址，再拼接文章 slug：

```text
http://127.0.0.1:24321/posts/my-first-firefly-article/
```

不要只检查详情页。首页、归档页和标签页也应该能找到这篇文章。如果文章没有出现，先确认 `draft: false`、文件位置、文件名和日期是否正确。

### 7. 用 Git 检查改动范围

文章显示正常后，执行：

```bash
git status --short
git diff -- src/content/posts/my-first-firefly-article.md
git diff --check
```

确认 Git 只显示计划中的 Markdown 文件和明确添加的图片，不要把构建产物、临时截图或编辑器缓存一并加入文章。

## 如何验证完成

按下面的最小清单逐项确认：

- [ ] 文章文件位于 `src/content/posts/`；
- [ ] 文件扩展名是 `.md` 或 `.mdx`；
- [ ] slug 只使用稳定、清晰的英文文件名；
- [ ] frontmatter 的开始和结束标记都是 `---`；
- [ ] `title`、`published`、`description`、`tags`、`category` 和 `draft` 已填写；
- [ ] `tags` 是数组，`draft` 是布尔值；
- [ ] 文章正文能够表达一个明确主题；
- [ ] `draft: false` 的文章能在首页或归档页找到；
- [ ] 文章详情页能够通过 slug 打开；
- [ ] 页面没有显示 Markdown 原始标记或空白内容；
- [ ] `pnpm check` 执行成功；
- [ ] `pnpm build` 执行成功；
- [ ] `git diff --check` 执行成功；
- [ ] `git diff` 中没有无关文件。

推荐按这个顺序执行验证：

```bash
pnpm check
pnpm build
git diff --check
```

只要文章页面、本地构建和 Git 差异都符合预期，这篇文章就已经完成了“可访问、可构建、可继续修改”的最小闭环。

## 失败时先检查什么

### `pnpm new-post` 提示文件已存在

说明目标 slug 已经被使用。先执行：

```bash
Get-ChildItem src/content/posts -File
```

确认是要编辑旧文章，还是换一个新的文件名。不要为了绕过提示直接删除旧文件。

### `pnpm check` 报 frontmatter 错误

先检查 `---` 是否成对，`published` 是否是合法日期，`tags` 是否使用数组格式，`draft` 是否使用布尔值。再检查 YAML 中是否有未闭合的引号。

### 文章详情页是 404

检查文件是否真的位于 `src/content/posts/`，浏览器地址中的 slug 是否与文件名完全一致，并确认开发服务器已经重新加载了新文件。文件名中的大小写和短横线都可能影响路径。

### 首页没有文章，但详情页可以打开

检查 `draft` 是否为 `false`，再检查发布时间是否晚于当前时间。草稿文章可能不会出现在公开文章列表中，未来时间也可能影响排序和展示。

### 页面显示但图片加载失败

确认图片路径是相对于 Markdown 文件的 `./images/...`，文件名大小写完全一致，图片确实保存在仓库中。不要直接引用电脑上的 `E:/` 路径。

### 构建失败或页面空白

先查看终端中的第一条错误，定位到文件和行号。把完整错误、刚刚修改的 Markdown 文件和 `git diff` 一起交给 AI 分析；不要只说“页面坏了”。修正后重新运行 `pnpm check` 和 `pnpm build`。

---

*从零开始学 AI 编程 · 阶段五 · Firefly 实战*
