---
title: "使用 GitHub Pages 部署 Firefly"
published: 2026-07-12T21:00:00+08:00
updated: 2026-07-12T21:00:00+08:00
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

## 具体怎么做

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

### 5. 打开 GitHub Actions 查看运行状态

进入自己的 GitHub 仓库，点击：

~~~text
Actions → Deploy to GitHub Pages
~~~

一次完整运行包含两个主要任务：

1. build：检出代码、安装 Node.js 和 pnpm、运行 pnpm run build、上传 Pages 构建产物；
2. deploy：使用 actions/deploy-pages 将构建产物发布到 GitHub Pages。

等待两个任务都显示绿色成功。只看到 build 成功，不代表网站已经完成部署；还要确认 deploy 任务也成功。

### 6. 必要时手动运行工作流

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

### 7. 检查 GitHub Pages 设置

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

### 8. 打开线上地址并验证

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

### 9. 认识后续更新循环

第一次部署成功后，更新网站只需要重复这条流程：

1. 修改文章或配置；
2. 本地运行 pnpm check；
3. 本地运行 pnpm build；
4. 创建 Git commit；
5. 推送到 master；
6. 等待 Deploy to GitHub Pages 完成；
7. 打开线上地址验证。

如果浏览器仍显示旧页面，可以先强制刷新，再确认 Actions 的最新运行时间和 GitHub 页面的最新 commit。

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

---

*从零开始学 AI 编程 · 阶段五 · Firefly 实战*
