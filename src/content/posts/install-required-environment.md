---
title: 安装必备环境
published: 2026-07-07T16:30:00+08:00
pinned: false
description: 搭建 Firefly 博客开发环境前，需要先安装 Node.js、pnpm、Git、GitHub Desktop 和 VS Code。本篇文章整理了下载安装到版本验证的完整流程。
tags: [Firefly, 环境配置, Node.js, pnpm, Git]
category: 博客指南
draft: false
image: ./images/nodejs-download.png
---

开始使用 Firefly 之前，建议先把本地开发环境准备好。下面这些工具覆盖了运行项目、管理依赖、拉取代码、提交代码和编辑文章的基础流程。

## 必备工具

- **Node.js**：关键运行环境，建议安装 LTS 稳定版，版本需要 `>= 22`。
- **pnpm**：项目使用的包管理工具，可以通过 `npm` 安装。
- **Git**：版本控制工具，用于拉取、提交代码。
- **GitHub Desktop**：GitHub 官方客户端，适合可视化管理仓库。
- **VS Code**：常用编辑器，用于修改配置和编写文章。

## 安装 Node.js

这里推荐安装 LTS 稳定版，版本需要 `>= 22`。可以直接下载 [Node.js v24.17.0 LTS](https://nodejs.org/zh-cn/download)，也可以打开 [Node.js 下载页面](https://nodejs.org/zh-cn/download) 手动选择适合您系统的安装包。

![下载 Node.js](./images/nodejs-download.png)

下载完成后运行安装程序，保持默认选项一路下一步即可。安装结束后，重新打开终端窗口，执行下面的命令验证版本号：

```bash title="Terminal window"
node -v
npm -v
```

![验证 Node.js 和 npm 版本号](./images/nodejs-version-check.png)

如果终端无法识别命令，先重启终端；仍然无效时，可以重启电脑后再验证。

## 安装 pnpm

Node.js 安装完成后，系统会同时提供 `npm`。打开终端，使用下面的命令全局安装 pnpm：

```bash title="Terminal window"
npm install -g pnpm
```

随后执行下面的命令验证安装结果：

```bash title="Terminal window"
pnpm -v
```

![安装并验证 pnpm](./images/pnpm-install-check.png)

看到版本号后，就可以使用 `pnpm` 安装项目依赖、启动开发服务和构建站点了。

## 安装 Git

访问 [Git 下载页面](https://git-scm.com/downloads)，下载最新版安装包。安装时使用默认选项即可，安装完成后可以在终端执行下面的命令确认版本：

![下载 Git for Windows](./images/git-download.png)

```bash title="Terminal window"
git -v
```

Git 安装完成后，就可以通过命令行或图形客户端克隆仓库、查看修改和提交代码。

## 安装 GitHub Desktop

访问 [GitHub Desktop 下载页面](https://desktop.github.com/) 下载官方客户端。安装完成后，打开 GitHub Desktop 并登录自己的 GitHub 账号。

![绑定 GitHub Desktop 账号](./images/github-desktop-sign-in.png)

绑定账号后，可以用它克隆 Firefly 仓库、查看文件改动、创建提交，也可以把本地修改推送到 GitHub。

## 安装 VS Code

如果您已经有更顺手的编辑器，可以继续使用原来的工具。若需要安装 VS Code，可以访问 [VS Code 下载页面](https://code.visualstudio.com/) 选择自己的系统版本。

![下载 VS Code](./images/vscode-download.png)

安装完成后，使用 VS Code 打开 Firefly 项目目录，就可以修改站点配置、编写 Markdown 文章，并通过内置终端运行项目命令。
