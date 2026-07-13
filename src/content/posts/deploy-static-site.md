---
title: "使用 GitHub Pages 部署 Firefly"
published: 2026-07-12T21:00:00+08:00
updated: 2026-07-13T22:00:00+08:00
pinned: false
description: "使用 Firefly 仓库自带的 GitHub Actions 工作流构建 Astro 网站，并发布到 GitHub Pages 项目站点。"
tags: [AI编程, Firefly, GitHub Pages, Astro, GitHub Actions]
category: 博客指南
draft: false
---

完成 [提交并推送到 GitHub](/posts/prepare-github-repository/) 后，代码已经进入 GitHub，但别人还不能通过网址访问网站。本篇使用 Firefly 仓库已有的 GitHub Actions 工作流，把项目构建成静态网站并发布到 GitHub Pages。

本篇的目标是跑通一条稳定闭环：

> 本地修改 → 提交并推送 → Actions 构建 → GitHub Pages 部署 → 打开线上页面验证。

## 问题是什么

本篇只解决一个问题：如何把已经推送到 GitHub 的 Firefly 仓库，部署成可以通过 github.io 访问的网站。

完成后应该得到：

- GitHub Actions 的构建任务成功；
- GitHub Pages 的部署任务成功；
- 线上首页能够打开；
- 线上文章页、资源和站内链接能够正常访问；
- 下一次向目标分支推送代码时，能够自动触发更新。

当前仓库的部署信息是：

| 项目 | 当前值 |
| --- | --- |
| 仓库 | a20260626/Firefly |
| 推送分支 | master |
| 工作流 | .github/workflows/deploy.yml |
| 构建命令 | pnpm run build |
| Node.js | 22 |
| pnpm | 9.14.4 |
| 线上地址 | https://a20260626.github.io/Firefly/ |

如果你的 GitHub 用户名或仓库名不同，线上地址要替换成自己的值。

## 适用环境是什么

本文适用于：

- 已经 Fork 并克隆 Firefly；
- 本地文章或配置已经完成；
- pnpm check 和 pnpm build 在本地通过；
- 改动已经提交并推送到 GitHub；
- 仓库默认分支和工作流中的触发分支一致；
- GitHub 账号拥有仓库设置和 Actions 的查看权限。

Firefly 是 Astro 静态网站。GitHub Actions 会在云端安装依赖、运行构建命令，再把构建结果交给 GitHub Pages 发布。电脑不需要一直开机，网站也不依赖本地 pnpm dev 进程。

## 先理解部署到底做了什么

第一次接触部署时，很容易把“代码已经在 GitHub”和“网站已经上线”当成同一件事。它们其实是两个阶段：

| 阶段 | 输入 | 输出 | 谁来完成 |
| --- | --- | --- | --- |
| 保存源码 | Markdown、Astro、Svelte、配置文件 | Git 仓库中的 commit | 你和 Git |
| 构建网站 | 仓库源码、依赖和构建配置 | `dist` 中的 HTML、CSS、JavaScript、图片 | Astro |
| 上传产物 | 构建后的静态文件 | Pages 构建产物 | GitHub Actions |
| 发布站点 | Pages 构建产物 | 可以公开访问的网址 | GitHub Pages |

可以把整个过程理解成：源码是“菜谱”，`pnpm build` 做出的 `dist` 是“成品”，GitHub Pages 负责把成品放到公开可访问的位置。访问者不会运行你的 Markdown，也不会在自己的电脑上启动 Astro；他们拿到的是已经生成好的静态页面。

这也解释了几个常见现象：

- `pnpm dev` 能打开，只能证明开发服务器可以工作，不代表生产构建一定成功；
- GitHub 上能看到新文章，只能证明源码已推送，不代表 Pages 已发布这次构建；
- build 成功但 deploy 失败，说明成品已经做出来，却还没有成功发布；
- deploy 成功但页面资源 404，通常是站点子路径、资源地址或缓存方面的问题。

## 部署前需要分清的三个地址

整个过程中会遇到三个用途不同的地址：

| 地址 | 用途 | 是否公开 |
| --- | --- | --- |
| `http://127.0.0.1:24321/` | 本地开发预览 | 只有当前电脑通常能访问 |
| `https://github.com/a20260626/Firefly` | 查看源码、commit 和 Actions | 由仓库可见性决定 |
| `https://a20260626.github.io/Firefly/` | 访问发布后的静态网站 | 通常公开访问 |

不要把本地预览地址发给读者。`127.0.0.1` 永远表示“访问者自己的电脑”，别人打开后不会连接到你的开发服务器。

GitHub Pages 又分为两种站点：

- 用户站点：仓库名是 `用户名.github.io`，网址通常是 `https://用户名.github.io/`；
- 项目站点：仓库名是其他名称，网址通常是 `https://用户名.github.io/仓库名/`。

Firefly 当前属于项目站点，所以 `/Firefly/` 不是装饰，而是所有线上页面和资源共同使用的基础路径。

## 具体怎么做

下面按第一次部署的实际顺序操作。已经成功部署过的读者，也可以用它检查自动发布链路是否完整。

### 1. 先检查部署工作流

打开项目中的：

~~~text
.github/workflows/deploy.yml
~~~

重点确认以下内容：

~~~yaml
on:
  push:
    branches: [master]
  workflow_dispatch:
~~~

这表示两种启动方式：

- 向 master 分支推送代码时自动运行；
- 在 GitHub Actions 页面手动点击运行。

再查看构建步骤：

~~~yaml
uses: withastro/action@v6
with:
  node-version: 22
  package-manager: pnpm@9.14.4
  build-cmd: pnpm run build
~~~

这就是线上环境使用的 Node.js、pnpm 和构建命令。线上配置应与本地环境尽量保持一致。

工作流文件不是普通说明文档，它决定 GitHub 在什么条件下启动、拥有什么权限、执行哪些任务。继续确认三个容易忽略的部分：

~~~yaml
permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false
~~~

- `contents: read` 允许工作流读取仓库源码；
- `pages: write` 允许它发布 Pages；
- `id-token: write` 用于 GitHub Pages 的安全身份验证；
- `concurrency` 避免多个 Pages 发布任务无序地同时执行。

YAML 对缩进敏感。修改工作流时不要随意删除空格或把 `deploy` 放进 `build` 的缩进层级。对初学者而言，如果现有工作流已经符合当前仓库，不需要为了“更现代”而随意替换 action 或版本。

### 2. 理解项目站点的地址

如果仓库名是 Firefly，它通常属于 GitHub Pages 的项目站点，地址包含仓库名：

~~~text
https://a20260626.github.io/Firefly/
~~~

工作流会根据仓库所有者和仓库名计算两个值：

- ASTRO_SITE：站点域名；
- ASTRO_BASE：项目子路径，例如 /Firefly。

因此本地地址和线上地址不同：

~~~text
本地：http://127.0.0.1:24321/
线上：https://a20260626.github.io/Firefly/
~~~

线上访问文章时，路径也要带上项目名：

~~~text
https://a20260626.github.io/Firefly/posts/write-first-code-with-ai/
~~~

不要把项目站点误写成 https://a20260626.github.io/posts/...，否则会缺少 /Firefly/ 前缀。

工作流中的 Resolve Pages URL 会自动区分用户站点和项目站点。以当前仓库为例，它最终得到：

~~~text
GITHUB_REPOSITORY_OWNER = a20260626
repo = Firefly
site = https://a20260626.github.io
base = /Firefly
~~~

`ASTRO_SITE` 告诉 Astro 正式域名是什么，`ASTRO_BASE` 告诉 Astro 网站位于域名下的哪个子目录。导航链接、样式表和图片地址若忽略 `base`，本地可能正常，部署后却会请求错误的根路径。

### 3. 部署前做本地检查

在项目根目录执行：

~~~bash
pnpm check
pnpm build
git diff --check
git status --short --branch
~~~

确认：

- Astro 检查没有错误；
- 构建命令退出成功；
- 文章页面已经被生成；
- 本次改动已经提交；
- 当前分支是工作流监听的分支；
- 没有把密钥、临时文件或不相关改动推送上去。

如果本地构建失败，不要先去 GitHub Pages 页面反复点击。先处理本地的第一条构建错误。

四条命令分别回答不同问题：

| 命令 | 主要回答的问题 |
| --- | --- |
| `pnpm check` | Astro、TypeScript 和内容结构有没有诊断错误 |
| `pnpm build` | 生产环境能否真正生成静态网站 |
| `git diff --check` | 改动中有没有多余空格或冲突标记等问题 |
| `git status --short --branch` | 当前分支、未提交文件和远程同步状态是什么 |

`pnpm build` 完成后，可以确认输出目录存在：

~~~bash
Get-ChildItem dist
~~~

在 macOS 或 Linux 上可以使用：

~~~bash
ls dist
~~~

通常应该看到 `index.html`、文章目录和静态资源目录。不要手动修改 `dist` 来修线上页面，因为下次构建会覆盖它；真正的修复应发生在 `src`、`public` 或项目配置中。

### 4. 确认代码已经推送到 GitHub

查看最近提交和远程地址：

~~~bash
git log -1 --oneline
git remote -v
git status --short --branch
~~~

确认 GitHub 网页上的最新 commit 与本地一致。如果只修改了电脑上的文件，没有执行 git push，GitHub Actions 看不到这次改动。

使用终端推送：

~~~bash
git push origin master
~~~

如果使用 GitHub Desktop，则点击 Push origin，等待同步完成后再打开 Actions 页面。

可以用提交哈希确认“本地版本”和“远程版本”是同一个版本：

~~~bash
git rev-parse --short HEAD
git rev-parse --short origin/master
~~~

两条命令输出一致，表示本地当前提交已经存在于你最近获取到的远程跟踪分支中。如果不一致，先运行 `git status --short --branch` 判断是尚未推送，还是远程有自己尚未拉取的提交，不要看到不同就直接强制推送。

### 5. 打开 GitHub Actions 查看运行状态

进入自己的 GitHub 仓库，点击：

~~~text
Actions → Deploy to GitHub Pages
~~~

一次完整运行包含两个主要任务：

1. build：检出代码、安装 Node.js 和 pnpm、运行 pnpm run build、上传 Pages 构建产物；
2. deploy：使用 actions/deploy-pages 将构建产物发布到 GitHub Pages。

等待两个任务都显示绿色成功。只看到 build 成功，不代表网站已经完成部署；还要确认 deploy 任务也成功。

Actions 页面常见状态含义如下：

| 状态 | 含义 | 此时怎么做 |
| --- | --- | --- |
| Queued | 正在等待运行器 | 稍等并刷新，不要重复触发很多次 |
| In progress | 正在安装、构建或部署 | 打开任务可查看当前步骤 |
| Success | 该次运行成功 | 继续检查 deploy 和线上页面 |
| Failure | 某一步退出失败 | 展开红色步骤，寻找第一条有效错误 |
| Cancelled | 运行被人工或并发规则取消 | 判断是否已有更新的运行替代它 |
| Skipped | 前置任务未成功或条件不满足 | 先处理它依赖的任务 |

打开某次运行后，先确认页面顶部显示的分支和 commit。否则你可能正在阅读旧提交的成功记录，却误以为最新内容已经发布。

### 6. 学会阅读一次工作流日志

当 build 失败时，按步骤名称从上到下检查：

1. **Checkout**：源码是否成功检出；
2. **Resolve Pages URL**：是否算出了正确的 `site` 和 `base`；
3. **Install, build, and upload**：依赖安装、Astro 构建或产物上传在哪一段失败；
4. **Deploy to GitHub Pages**：是否有 Pages 权限、环境或发布错误。

日志末尾的 `Process completed with exit code 1` 只表示命令失败，不是根因。向上寻找最早出现的具体文件名、行号和错误消息。例如：

~~~text
src/content/posts/example.md: frontmatter does not match collection schema
~~~

这类错误已经指出问题在文章的 frontmatter，应先修正文档，而不是修改 Pages 设置。

修复后按下面的闭环重新操作：

~~~text
本地复现 → 修改源码 → pnpm check → pnpm build → commit → push → 查看新的 Actions 运行
~~~

不要直接在旧运行上反复点击 Re-run jobs 来期待源码自动变化；重新运行使用的仍是那一次运行对应的 commit。

### 7. 必要时手动运行工作流

如果推送后没有看到新的运行记录，先检查：

- 推送的分支是否是 master；
- .github/workflows/deploy.yml 是否已经推送到 GitHub；
- Actions 是否被仓库设置禁用；
- 页面是否仍在加载旧的运行记录。

确认工作流存在后，在 Actions 页面选择 Deploy to GitHub Pages，点击 Run workflow，选择 master，再点击运行。

如果电脑已经安装并登录 GitHub CLI，也可以执行：

~~~bash
gh workflow run deploy.yml --repo a20260626/Firefly --ref master
~~~

手动运行只是在没有自动触发时补启动作，不会替代构建和部署验证。

### 8. 检查 GitHub Pages 设置

进入仓库：

~~~text
Settings → Pages
~~~

在 Build and deployment 中确认发布来源使用：

~~~text
Source: GitHub Actions
~~~

不要同时选择从分支部署和工作流部署。当前 deploy.yml 使用的是 GitHub Actions，并且声明了：

~~~yaml
permissions:
  contents: read
  pages: write
  id-token: write
~~~

这些权限用于读取源码、上传 Pages 构建产物和完成部署。如果仓库权限设置过于严格，工作流可能在部署阶段失败。

第一次启用 Pages 后，GitHub 可能需要一点时间创建站点环境。部署完成页通常会显示实际访问地址；优先使用该地址验证，而不是凭记忆拼写。仓库如果是私有仓库，还要确认当前 GitHub 账户套餐与组织策略是否允许所需的 Pages 可见性。

### 9. 打开线上地址并验证

部署任务成功后，打开：

~~~text
https://a20260626.github.io/Firefly/
~~~

依次检查：

- 首页标题和文章列表；
- 最新的“提交并推送到 GitHub”文章；
- 一篇旧文章；
- 文章内的目录和上一篇/下一篇链接；
- 页面图片、字体和样式；
- 直接刷新文章详情页；
- 浏览器开发者工具中是否有 404 资源；
- 手机宽度下是否有明显横向溢出。

本次文章的线上地址应为：

~~~text
https://a20260626.github.io/Firefly/posts/prepare-github-repository/
~~~

如果首页正常、文章页也正常，说明站点路径和内容构建基本正确。

建议把验证分成四层，而不是只看首页能否打开：

1. **可达性**：首页返回正常页面，不是 GitHub 的 404；
2. **路由**：从首页进入文章页，并直接刷新文章页；
3. **资源**：图片、CSS、字体和脚本没有 404；
4. **内容**：最新文章标题、更新时间和正文确实属于最新 commit。

为什么要检查最后一层？因为旧部署也可能完整可用。只有看到本次修改的特征内容，才能证明最新版本已经上线。

### 10. 认识后续更新循环

第一次部署成功后，更新网站只需要重复这条流程：

1. 修改文章或配置；
2. 本地运行 pnpm check；
3. 本地运行 pnpm build；
4. 创建 Git commit；
5. 推送到 master；
6. 等待 Deploy to GitHub Pages 完成；
7. 打开线上地址验证。

如果浏览器仍显示旧页面，可以先强制刷新，再确认 Actions 的最新运行时间和 GitHub 页面的最新 commit。

日常小修改不需要重新配置 Pages，也不需要重新创建仓库。只要工作流、目标分支和 Pages 来源没有变化，后续每次 push 都会复用同一条发布流水线。

## 如何确认线上内容对应哪个提交

部署排错的核心不是“多刷新几次”，而是建立版本对应关系：

~~~text
本地 HEAD
  ↓ git push
GitHub 仓库中的 commit
  ↓ workflow run
Actions 运行关联的 commit
  ↓ deploy
线上页面显示的内容
~~~

至少核对下面三处：

- 本地 `git log -1 --oneline` 的提交哈希；
- GitHub 仓库首页最新提交的哈希；
- Actions 最新成功运行所关联的提交哈希。

三者一致后，再在线上寻找本次新增的一句文字或最新文章。这样可以区分“代码没推送”“新工作流没运行”“部署尚未完成”和“浏览器缓存旧页面”四种完全不同的问题。

如果使用 GitHub CLI，可以查看最近运行：

~~~bash
gh run list --workflow deploy.yml --repo a20260626/Firefly --limit 5
~~~

还可以查看某次运行的详细信息和失败日志：

~~~bash
gh run view 运行编号 --repo a20260626/Firefly
gh run view 运行编号 --repo a20260626/Firefly --log-failed
~~~

`运行编号` 要替换为 `gh run list` 显示的实际编号。没有安装或登录 GitHub CLI 时，直接使用网页完成同样的检查即可。

## 如何验证完成

按下面的清单逐项确认：

- [ ] .github/workflows/deploy.yml 已存在；
- [ ] 工作流监听正确的分支；
- [ ] 工作流使用 Node.js 22；
- [ ] 工作流使用 pnpm 9.14.4；
- [ ] 工作流执行 pnpm run build；
- [ ] 本地 pnpm check 执行成功；
- [ ] 本地 pnpm build 执行成功；
- [ ] 最新 commit 已推送到 GitHub；
- [ ] Actions 的 build 任务成功；
- [ ] Actions 的 deploy 任务成功；
- [ ] GitHub Pages 来源设置为 GitHub Actions；
- [ ] https://a20260626.github.io/Firefly/ 可以打开；
- [ ] 线上文章详情页可以打开；
- [ ] 文章中的图片、样式和链接没有明显 404；
- [ ] 直接刷新文章页不会变成 404；
- [ ] 手机宽度下页面没有明显横向溢出。

推荐的验证顺序：

~~~bash
pnpm check
pnpm build
git push origin master
gh run list --workflow deploy.yml --repo a20260626/Firefly --limit 3
~~~

gh run list 不是必需命令，也可以直接在 GitHub 的 Actions 页面查看。

## 失败时先检查什么

排错时先判断故障发生在哪一层：

| 现象 | 更可能出问题的层级 |
| --- | --- |
| 没有新的 Actions 记录 | push、触发分支或工作流配置 |
| build 红色 | 依赖、内容、类型或 Astro 构建 |
| build 绿色、deploy 红色 | Pages 来源、权限或环境 |
| deploy 绿色、整个网站 404 | Pages 地址、站点初始化或仓库设置 |
| 首页正常、图片和样式 404 | `base` 或资源路径 |
| 只有新文章 404 | 文件未提交、`draft`、slug 或构建内容 |
| 页面正常但内容旧 | commit 对应关系、运行顺序或浏览器缓存 |

### Actions 没有运行

先检查推送分支是否为 master，再确认 GitHub 上是否存在 .github/workflows/deploy.yml。如果工作流存在但没有自动启动，可以使用页面上的 Run workflow 手动运行。

### build 任务失败

打开失败任务的日志，先看第一条真正的错误。常见原因包括依赖安装失败、Markdown frontmatter 错误、图片路径错误、TypeScript 错误和构建时远程请求失败。先在本地重新运行：

~~~bash
pnpm check
pnpm build
~~~

不要只看日志最后一行。

### deploy 任务失败

先检查仓库 Settings → Pages 的来源是否是 GitHub Actions，再检查工作流是否保留了 pages: write 和 id-token: write 权限。部署权限不足时，通常是 build 成功、deploy 失败。

### 首页打开但资源 404

检查访问地址是否包含仓库名 /Firefly/。项目站点不能把资源根路径简单当成域名根路径。确认工作流设置了 ASTRO_BASE，并重新运行一次工作流。

### 首页打开但文章页 404

先确认文章页面是否在构建输出中生成，再确认网址包含正确的项目路径和文章 slug：

~~~text
/Firefly/posts/文章文件名/
~~~

也要检查 Markdown 文件是否已经提交并推送，draft 是否为 false。

### 推送后线上仍是旧内容

检查 GitHub 上的最新 commit 是否包含本次改动，再检查 Actions 是否已经完成最新一次部署。部署成功后先强制刷新浏览器；若仍旧，使用无痕窗口打开线上地址排除缓存影响。

### 线上地址打不开

确认仓库 Pages 设置已经保存，Actions 的 deploy 任务为成功，并使用仓库所有者和仓库名组成正确地址：

~~~text
https://用户名.github.io/仓库名/
~~~

如果仓库名本身是 用户名.github.io，才使用没有仓库子路径的用户站点地址。

### 工作流一直排队

先等待几分钟并刷新运行详情。GitHub 托管运行器繁忙时，Queued 不一定表示配置错误。再检查仓库的 Actions 是否被禁用、账户是否有用量或策略限制，以及是否有较早的 Pages 任务占用同一个并发组。

不要连续点击 Run workflow 制造更多排队任务。保留最新 commit 对应的一次运行，取消明显过时的重复运行即可。

### 构建时提示 lockfile 或依赖错误

Firefly 使用 pnpm，工作流也明确指定 pnpm 版本。确认 `pnpm-lock.yaml` 已提交，并且没有用 npm 或 Yarn 重新生成其他锁文件。先在本地使用仓库要求的 pnpm 版本运行：

~~~bash
pnpm install
pnpm check
pnpm build
~~~

如果本地依赖目录长期未更新而云端失败，不要提交 `node_modules`；应修复 `package.json` 与 `pnpm-lock.yaml` 的一致性。

### 自定义域名后访问异常

自定义域名属于首次 github.io 部署成功之后的增强配置。应先保证默认 Pages 地址完整可用，再在 Settings → Pages 中配置 Custom domain，并按照域名服务商要求添加 DNS 记录。

配置后需要等待 DNS 生效，并分别检查自定义域名和默认项目地址。不要为了修复 DNS 问题去修改文章内容；域名解析、HTTPS 证书和 Astro 的 `site` 配置是不同层级的问题。

## 给 AI 的排错信息应该怎么写

如果需要让 AI 帮忙，不要只说“部署失败了”。提供下面这些信息，定位会快很多：

~~~text
仓库：a20260626/Firefly
分支：master
本地 pnpm check：成功 / 失败
本地 pnpm build：成功 / 失败
Actions 运行对应的 commit：填写短哈希
失败任务：build / deploy
失败步骤：填写步骤名
第一条具体错误：粘贴关键错误，不要粘贴密钥
线上现象：首页 404 / 资源 404 / 内容未更新 / 其他
已经尝试：列出做过的检查
~~~

这份信息能帮助 AI 先判断问题属于源码、构建、权限、路径还是缓存。发送日志前要检查其中是否包含令牌、私有地址、邮箱或其他敏感信息。

---

*从零开始学 AI 编程 · 阶段五 · Firefly 实战*
