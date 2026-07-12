---
title: "Fork 和克隆 Firefly"
published: 2026-07-12T17:00:00+08:00
updated: 2026-07-12T17:00:00+08:00
pinned: false
description: "从 GitHub Fork Firefly 官方仓库，再使用 GitHub Desktop 克隆到 Windows 本地，并验证远程仓库、项目目录和开发环境是否准备完成。"
tags: [AI编程, Firefly, GitHub, Fork, GitHub Desktop, 博客搭建]
category: 博客指南
draft: false
image: ./images/fork-firefly-repository.png
---

完成 AI 编程工具安装后，下一步是把 Firefly 变成自己的项目副本。这个过程包含两个动作：先在 GitHub 上 Fork 官方仓库，再把自己的仓库克隆到本地电脑。

本篇只处理“获得属于自己的 Firefly 代码并放到本地”这一件事。依赖安装和本地启动只作为最后的基础验证，具体运行问题留到后续文章处理。

## 问题是什么

“我想使用 Firefly”还不是一个可以直接执行的问题。需要把目标写成下面这样：

> 我已经安装好 Git、GitHub Desktop、Node.js 和 pnpm。现在请把 `CuteLeaf/Firefly` Fork 到我的 GitHub 账号，再克隆到 Windows 本地目录，并验证本地目录确实连接到我的仓库。

这一步的输入、输出和边界如下：

| 要素 | 本篇内容 |
| --- | --- |
| 目标 | 获得自己的 Firefly GitHub 仓库和本地项目目录 |
| 官方来源 | `CuteLeaf/Firefly` |
| 本地工具 | GitHub Desktop、Git、VS Code 或文件管理器 |
| 本地输出 | 包含 `package.json` 和 `.git` 的 Firefly 项目目录 |
| 远程输出 | `https://github.com/你的用户名/Firefly` |
| 验收标准 | 远程仓库属于自己，本地目录能看到项目文件，远程地址指向自己的仓库 |

Fork 和克隆不是同一个动作：Fork 发生在 GitHub 服务器上，克隆发生在自己的电脑上。只完成其中一个，流程都还没有结束。

## 适用环境是什么

本文适用于下面的环境：

- Windows 10 或 Windows 11。
- 已经拥有可以正常登录的 GitHub 账号。
- 已经安装并登录 GitHub Desktop。
- 已经安装 Git、Node.js 和 pnpm。
- 已经准备好一个用于存放代码的本地目录。

开始前，建议先完成上一篇：[安装 AI 编程工具](/posts/install-required-environment/)。如果在 GitHub Desktop 中看不到仓库，先检查登录账号是否正确，不要直接重复创建多个 Fork。

这几个地址的作用不同：

| 地址或目录 | 作用 |
| --- | --- |
| `https://github.com/CuteLeaf/Firefly` | 官方仓库，只作为 Fork 来源 |
| `https://github.com/你的用户名/Firefly` | 自己的远程仓库，后续提交和推送都使用它 |
| `E:\Documents\GitHub\Firefly` | 本地项目目录，用编辑器和终端操作 |

## 具体怎么做

### 1. 打开 Firefly 官方仓库

在浏览器中打开：

[https://github.com/CuteLeaf/Firefly](https://github.com/CuteLeaf/Firefly)

进入仓库后，先确认页面显示的所有者是 `CuteLeaf`，仓库名称是 `Firefly`。不要把别人的 Fork 或搜索结果中的同名仓库当成官方来源。

![打开 Firefly 官方仓库](./images/fork-firefly-repository.png)

### 2. 创建自己的 Fork

点击仓库页面右上方的 **Fork**，进入创建 Fork 页面。

逐项检查：

- **Owner**：选择自己的 GitHub 账号。
- **Repository name**：默认使用 `Firefly` 即可，也可以使用自己清楚的项目名称。
- **Description**：可以保留官方描述，也可以改成自己的说明。
- **Copy the master branch only**：初学者保持默认即可。

确认 Owner 不是 `CuteLeaf`，而是自己的账号，然后点击 **Create fork**。

![填写 Fork 信息并创建仓库](./images/create-firefly-fork.png)

Fork 成功后，浏览器地址应该类似：

```text
https://github.com/你的用户名/Firefly
```

这一刻只代表远程副本创建完成，代码还没有出现在自己的电脑里。

### 3. 确认远程仓库属于自己

在自己的仓库页面检查下面三项：

- 页面标题中包含自己的 GitHub 用户名。
- 仓库名称是 `Firefly`。
- 文件列表中能看到 `package.json`、`src` 和 `.github` 等项目内容。

如果页面顶部显示类似 “forked from CuteLeaf/Firefly”，这是正常信息，它说明这个仓库来源于官方项目，并不代表你还在修改官方仓库。

### 4. 使用 GitHub Desktop 克隆仓库

打开 GitHub Desktop，选择 **File → Clone repository**。在仓库列表中找到自己账号下的 `Firefly`，确认 Owner 是自己的账号，然后点击 **Clone**。

![在 GitHub Desktop 中搜索并克隆 Firefly](./images/github-desktop-clone-firefly.png)

在 **Local path** 中选择本地保存位置，例如：

```text
E:\Documents\GitHub\Firefly
```

建议路径中不要包含临时目录或多个同名嵌套目录。选择完成后点击 **Clone**，等待 GitHub Desktop 将代码下载完成。

### 5. 检查本地项目目录

克隆完成后，在 GitHub Desktop 中点击 **Show in Explorer**，打开本地目录。项目根目录至少应该能看到：

```text
Firefly/
├─ .github/
├─ public/
├─ src/
├─ package.json
├─ pnpm-lock.yaml
└─ README.md
```

然后在该目录中打开 PowerShell，执行：

```powershell title="确认当前目录"
Get-Location
Get-ChildItem package.json, pnpm-lock.yaml
```

如果两个文件都能找到，说明当前终端位于项目根目录。

### 6. 检查远程地址

继续在项目根目录执行：

```powershell title="检查 Git 远程仓库"
git remote -v
git status
```

`origin` 应该指向自己的仓库，例如：

```text
origin  https://github.com/你的用户名/Firefly.git (fetch)
origin  https://github.com/你的用户名/Firefly.git (push)
```

如果 `origin` 指向 `CuteLeaf/Firefly`，不要继续提交。先修改为自己的仓库地址：

```powershell
git remote set-url origin https://github.com/你的用户名/Firefly.git
```

### 7. 安装依赖并做基础启动检查

确认目录和远程地址都正确后，安装项目依赖：

```powershell title="安装 Firefly 依赖"
pnpm install
```

依赖安装完成后，启动开发服务：

```powershell title="启动 Firefly"
pnpm dev
```

以终端实际输出的地址为准，在浏览器中打开本地页面。看到 Firefly 首页后，可以在终端使用 `Ctrl+C` 停止服务。

这一步只用于确认克隆结果可以进入下一阶段，不在本篇展开页面配置和运行故障排查。

## 如何验证完成

依次完成下面的检查：

- [ ] 浏览器打开的是 `CuteLeaf/Firefly` 官方仓库。
- [ ] Fork 的 Owner 是自己的 GitHub 账号。
- [ ] 自己的 GitHub 仓库地址不是 `CuteLeaf/Firefly`。
- [ ] GitHub Desktop 已经克隆自己的 `Firefly` 仓库。
- [ ] 本地项目根目录中存在 `package.json`。
- [ ] 本地项目根目录中存在 `pnpm-lock.yaml`。
- [ ] 当前终端目录就是 Firefly 根目录。
- [ ] `git remote -v` 中的 `origin` 指向自己的仓库。
- [ ] `git status` 能正常执行。
- [ ] `pnpm install` 执行完成。
- [ ] `pnpm dev` 能启动本地服务。
- [ ] 浏览器能打开 Firefly 本地首页。

最后做一次闭环确认：在 GitHub Desktop 中查看当前仓库名称和账号，在文件管理器中查看本地目录，再在终端执行 `git remote -v`。三个地方都指向自己的 Firefly，才算完成。

## 失败时先检查什么

### GitHub 页面没有 Fork 按钮

先检查当前是否登录 GitHub，以及当前账号是否有创建仓库的权限。如果页面打开的是自己的 Fork，就不需要再次 Fork，可以直接进入自己的仓库页面。

### Fork 创建后找不到仓库

确认 GitHub 页面右上角登录账号与 Fork 时选择的 Owner 一致。在 GitHub Desktop 中退出错误账号并重新登录，再刷新仓库列表。

### GitHub Desktop 中没有 Firefly

点击仓库列表的刷新按钮，或者使用 **Clone repository → URL**，粘贴自己的仓库地址：

```text
https://github.com/你的用户名/Firefly.git
```

不要粘贴官方仓库地址，否则本地 `origin` 会指向错误的远程仓库。

### Clone 按钮不可用

检查本地路径是否存在、是否有写入权限，以及目标目录是否已经包含另一个项目。如果目录中已经有同名 Firefly，选择一个新的空目录，避免项目嵌套。

### `git remote -v` 指向官方仓库

这是最需要先处理的问题。执行下面的命令替换 `origin`，再重新检查：

```powershell
git remote set-url origin https://github.com/你的用户名/Firefly.git
git remote -v
```

### `pnpm install` 或 `pnpm dev` 失败

先执行：

```powershell
node --version
pnpm --version
Get-Location
```

确认 Node.js 版本符合项目要求、pnpm 可以识别，并且当前目录包含 `package.json`。如果仍然失败，保留完整错误信息，再单独处理“安装依赖”或“启动项目”问题。

完成本篇后，下一步可以继续学习项目结构和配置修改：[认识 Firefly 文件结构并修改配置](/posts/setup-project-foundation/)。
