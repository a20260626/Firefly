---
title: "Fork 和克隆 Firefly"
published: 2026-07-12T17:00:00+08:00
updated: 2026-07-13T13:30:00+08:00
pinned: false
description: "从 GitHub Fork Firefly 官方仓库，再使用 GitHub Desktop 克隆到 Windows 本地，并验证远程仓库、项目目录和开发环境是否准备完成。"
tags: [AI编程, Firefly, GitHub, Fork, GitHub Desktop, 博客搭建]
category: 博客指南
draft: false
image: ./images/fork-firefly-repository.png
---

完成 AI 编程工具安装后，下一步是把 Firefly 变成自己的项目副本。这个过程包含两个动作：先在 GitHub 上 Fork 官方仓库，再把自己的仓库克隆到本地电脑。

本篇只处理“获得属于自己的 Firefly 代码并放到本地”这一件事。依赖安装和本地启动只作为最后的基础验证，具体运行问题留到后续文章处理。

## 先理解 Fork、Clone 和远程仓库

开始点击按钮之前，先把几个容易混淆的概念分开。

### Fork：在 GitHub 上创建自己的副本

Fork 发生在 GitHub 网站上。它会在你的账号下创建一个新仓库，并保留“来源于官方仓库”的关系。

```text
官方仓库：CuteLeaf/Firefly
        ↓ Fork
你的仓库：你的用户名/Firefly
```

Fork 之后，你可以向自己的仓库推送提交，而不需要拥有官方仓库的写入权限。你的修改不会自动改变官方项目。

### Clone：把远程仓库下载到电脑

Clone 发生在本地电脑上。它不仅下载当前文件，还会创建 `.git` 目录，保存提交历史、分支和远程仓库信息。

```text
你的 GitHub 仓库
        ↓ Clone
电脑上的 Firefly 文件夹
```

直接在网页上下载 ZIP 只能得到一份文件快照，没有完整 Git 历史，也不会自动连接到你的远程仓库，因此不适合后续提交和推送流程。

### origin 和 upstream：两个不同的远程名称

Git 使用简短名称记录远程仓库：

| 名称 | 通常指向 | 主要用途 |
| --- | --- | --- |
| `origin` | 你自己的 Fork | 拉取自己的提交、推送修改 |
| `upstream` | `CuteLeaf/Firefly` 官方仓库 | 查看和同步官方更新 |

克隆自己的 Fork 后，GitHub Desktop 通常只创建 `origin`。`upstream` 不是开始写博客的必要条件，可以等需要同步官方更新时再添加。

整个关系可以画成：

```text
CuteLeaf/Firefly（官方）
        ↓ Fork
你的用户名/Firefly（自己的远程仓库，origin）
        ↓ Clone
本地 Firefly 目录（实际编辑文件的位置）
```

理解这三层后，遇到“我到底改的是哪一份代码”时，就能分别检查网页仓库、远程地址和本地目录。

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

## 操作前的准备检查

开始 Fork 前完成下面的检查：

- [ ] 浏览器中已经登录准备使用的 GitHub 账号。
- [ ] GitHub Desktop 登录的是同一个账号。
- [ ] 本地 Git 能通过 `git --version` 输出版本号。
- [ ] 已选择一个长期使用的代码存放目录。
- [ ] 目标磁盘有足够空间存放源码、依赖和构建产物。
- [ ] 目标目录不是桌面临时目录、下载目录或云盘同步冲突目录。
- [ ] 当前没有另一个需要保留的同名 `Firefly` 文件夹。

推荐使用结构清晰的路径，例如：

```text
D:\Projects\Firefly
E:\Documents\GitHub\Firefly
```

路径中可以包含空格和中文，但某些脚本或旧工具对复杂路径兼容性较差。初学阶段使用短、稳定、容易输入的路径，可以减少与项目本身无关的问题。

## 具体怎么做

### 1. 打开 Firefly 官方仓库

在浏览器中打开：

[https://github.com/CuteLeaf/Firefly](https://github.com/CuteLeaf/Firefly)

进入仓库后，先确认页面显示的所有者是 `CuteLeaf`，仓库名称是 `Firefly`。不要把别人的 Fork 或搜索结果中的同名仓库当成官方来源。

![打开 Firefly 官方仓库](./images/fork-firefly-repository.png)

判断来源时不要只看页面标题。至少核对三项：浏览器地址中的所有者是 `CuteLeaf`、仓库名是 `Firefly`、页面能看到正常的项目文件和提交记录。如果地址属于其他用户名，即使仓库也叫 Firefly，也可能是其他人的 Fork。

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

如果账号下已经存在名为 `Firefly` 的仓库，GitHub 可能不允许使用同名仓库。先打开现有仓库确认它是否就是之前创建的 Fork。不要立刻删除它，也不要连续创建 `Firefly-1`、`Firefly-2`；先判断是否可以继续使用已有仓库。

### 3. 确认远程仓库属于自己

在自己的仓库页面检查下面三项：

- 页面标题中包含自己的 GitHub 用户名。
- 仓库名称是 `Firefly`。
- 文件列表中能看到 `package.json`、`src` 和 `.github` 等项目内容。

如果页面顶部显示类似 “forked from CuteLeaf/Firefly”，这是正常信息，它说明这个仓库来源于官方项目，并不代表你还在修改官方仓库。

还可以检查默认分支。Firefly 当前流程使用 `master`，后续 GitHub Actions 也会监听指定分支。分支名称应以仓库页面和本地实际结果为准，不要因为其他教程使用 `main` 就随意重命名。

### 4. 使用 GitHub Desktop 克隆仓库

打开 GitHub Desktop，选择 **File → Clone repository**。在仓库列表中找到自己账号下的 `Firefly`，确认 Owner 是自己的账号，然后点击 **Clone**。

![在 GitHub Desktop 中搜索并克隆 Firefly](./images/github-desktop-clone-firefly.png)

在 **Local path** 中选择本地保存位置，例如：

```text
E:\Documents\GitHub\Firefly
```

建议路径中不要包含临时目录或多个同名嵌套目录。选择完成后点击 **Clone**，等待 GitHub Desktop 将代码下载完成。

克隆时 GitHub Desktop 会执行三个关键动作：下载提交历史、创建本地工作目录、把 `origin` 设置为刚才选择的仓库。进度完成前不要关闭应用，也不要同时在文件管理器中移动目标文件夹。

克隆结束后，GitHub Desktop 顶部应显示当前仓库为 `Firefly`，当前分支通常为 `master`。此时先不要修改文件，先完成目录和远程地址验证。

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

还可以执行：

```powershell title="检查 Git 仓库和当前分支"
git rev-parse --show-toplevel
git branch --show-current
git status --short --branch
```

- `git rev-parse --show-toplevel` 应输出 Firefly 项目根目录。
- `git branch --show-current` 应输出当前分支名称。
- `git status --short --branch` 应显示分支状态，并且刚克隆后通常没有文件变更。

`.git` 默认是隐藏目录，不必为了确认仓库而修改 Windows 显示设置。只要上述 Git 命令能成功，就证明当前目录属于一个 Git 仓库。

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

`fetch` 地址用于从远程读取，`push` 地址用于上传本地提交。两行通常都应指向你的 Fork。检查时要看完整的“用户名/仓库名”，不要只看到 `Firefly.git` 就认为正确。

### 7. 可选：添加官方仓库为 upstream

这一步不是首次运行 Firefly 的必要条件。如果你准备长期维护 Fork，可以添加官方仓库：

```powershell title="添加并检查 upstream"
git remote add upstream https://github.com/CuteLeaf/Firefly.git
git remote -v
```

添加后应看到两组远程：

```text
origin    https://github.com/你的用户名/Firefly.git
upstream  https://github.com/CuteLeaf/Firefly.git
```

不要把二者反过来。日常推送仍然推向 `origin`。同步官方更新涉及拉取、合并和冲突处理，应该作为独立任务，不要在首次克隆时顺手执行不理解的合并命令。

### 8. 安装依赖并做基础启动检查

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

首次安装依赖可能需要一段时间，并会在项目中生成 `node_modules`。终端持续输出下载和链接信息通常是正常现象。不要因为几秒钟没有新输出就反复按回车或同时启动第二次安装。

启动成功后，`pnpm dev` 会持续占用终端。需要停止时按一次 `Ctrl+C`，等待终端返回提示符。直接关闭整个终端也能停止进程，但看不到正常的结束状态。

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

如果添加了 `upstream`，再检查：

- [ ] `origin` 指向自己的 Fork。
- [ ] `upstream` 指向 `CuteLeaf/Firefly`。
- [ ] 没有把官方仓库设置成默认推送目标。

最后做一次闭环确认：在 GitHub Desktop 中查看当前仓库名称和账号，在文件管理器中查看本地目录，再在终端执行 `git remote -v`。三个地方都指向自己的 Firefly，才算完成。

### 保存一份克隆记录

建议把关键结果记录在学习笔记中：

```text
官方仓库：https://github.com/CuteLeaf/Firefly
我的 Fork：https://github.com/我的用户名/Firefly
本地目录：E:\Documents\GitHub\Firefly
当前分支：master
origin：https://github.com/我的用户名/Firefly.git
upstream：未添加 / https://github.com/CuteLeaf/Firefly.git
package.json：已找到
pnpm-lock.yaml：已找到
依赖安装：成功 / 未执行
本地首页：实际访问地址
检查日期：
```

不要照抄示例路径和用户名。记录真实结果以后，AI 才能判断“网页仓库、本地目录和远程地址”是否一致。

### 做一次无修改验证

刚克隆完成时执行：

```powershell
git status
```

理想结果是工作区没有需要提交的修改。如果还没有编辑任何文件，却出现大量改动，先不要提交。可能原因包括换行符设置、文件权限、自动格式化工具或安全软件改写文件。先用 `git diff --stat` 查看影响范围，再决定如何处理。

## 失败时先检查什么

### GitHub 页面没有 Fork 按钮

先检查当前是否登录 GitHub，以及当前账号是否有创建仓库的权限。如果页面打开的是自己的 Fork，就不需要再次 Fork，可以直接进入自己的仓库页面。

如果仓库页面提示 Fork 已存在，可以点击自己的 Fork 链接继续，不需要删除后重建。一个账号通常只需要维护一个来源明确的 Firefly Fork。

### Fork 创建后找不到仓库

确认 GitHub 页面右上角登录账号与 Fork 时选择的 Owner 一致。在 GitHub Desktop 中退出错误账号并重新登录，再刷新仓库列表。

还可以直接打开：

```text
https://github.com/你的用户名?tab=repositories
```

在自己的仓库列表中查找 Firefly。如果 Fork 创建在组织账号下，应使用组织名称替换“你的用户名”，并确认自己拥有克隆和推送权限。

### GitHub Desktop 中没有 Firefly

点击仓库列表的刷新按钮，或者使用 **Clone repository → URL**，粘贴自己的仓库地址：

```text
https://github.com/你的用户名/Firefly.git
```

不要粘贴官方仓库地址，否则本地 `origin` 会指向错误的远程仓库。

如果通过 URL 克隆，粘贴前先在浏览器打开该地址，确认页面所有者是自己。GitHub Desktop 能成功克隆并不代表仓库选择正确；公开的官方仓库同样可以被克隆。

### Clone 按钮不可用

检查本地路径是否存在、是否有写入权限，以及目标目录是否已经包含另一个项目。如果目录中已经有同名 Firefly，选择一个新的空目录，避免项目嵌套。

常见的错误目录结构是：

```text
E:\Documents\GitHub\Firefly\Firefly\package.json
```

这通常是把仓库又克隆进了一个手动创建的同名文件夹。虽然项目仍可能运行，但路径容易混淆。克隆前应在 **Local path** 中确认最终生成的项目目录只出现一次 Firefly。

### 克隆速度慢或中途失败

先保留 GitHub Desktop 显示的完整错误，不要连续点击 Clone 创建多个半成品目录。按顺序检查：

1. 浏览器是否能正常打开自己的仓库页面。
2. 网络连接是否稳定。
3. 目标磁盘是否有足够空间。
4. 目标目录是否仍为空、是否有写入权限。
5. 安全软件是否明确拦截了 GitHub Desktop 或 Git。

失败后如果目标目录留下不完整文件，不要直接在其中继续安装依赖。先确认该目录中是否有需要保留的个人文件，再清理失败副本并重新克隆到空目录。

### GitHub Desktop 显示错误账号

不要在账号不明确时继续 Fork、克隆或推送。先在浏览器和 GitHub Desktop 中分别核对用户名。若需要切换账号，完成重新登录后再刷新仓库列表，并重新确认远程地址。

Git 提交身份和 GitHub 登录账号也是两个概念：提交中的姓名、邮箱来自 Git 配置，推送权限来自 GitHub 登录或凭据。以后如果提交作者显示不正确，应检查 Git 配置，而不是重新 Fork 仓库。

### `git remote -v` 指向官方仓库

这是最需要先处理的问题。执行下面的命令替换 `origin`，再重新检查：

```powershell
git remote set-url origin https://github.com/你的用户名/Firefly.git
git remote -v
```

修改后必须再次查看结果。`git remote set-url` 没有输出通常表示命令执行完成，但只有新的远程地址才是验收证据。

如果仓库同时存在 `origin` 和 `upstream`，不要删除名称来“简化”问题。先逐行确认它们的角色是否正确。

### 终端提示“不是 Git 仓库”

这通常表示当前目录不在克隆后的项目中。执行：

```powershell
Get-Location
Get-ChildItem
```

检查当前位置和文件列表，再使用 `Set-Location` 进入真正包含 `package.json` 的 Firefly 目录。不要在随意找到的 `.git` 目录中直接执行命令。

### 本地存在多个 Firefly 目录

先不要凭修改时间猜哪个是正确项目。分别记录每个目录的：

```powershell
Get-Location
git remote -v
git status --short --branch
```

能够明确连接到自己 Fork、包含预期修改并由 GitHub Desktop 打开的目录，才是当前工作目录。确认前不要删除其他目录，避免丢失尚未推送的修改。

### `pnpm install` 或 `pnpm dev` 失败

先执行：

```powershell
node --version
pnpm --version
Get-Location
```

确认 Node.js 版本符合项目要求、pnpm 可以识别，并且当前目录包含 `package.json`。如果仍然失败，保留完整错误信息，再单独处理“安装依赖”或“启动项目”问题。

### 推送时提示没有权限

首先检查 `git remote -v`。如果 `origin` 指向官方仓库或其他人的 Fork，没有权限是正常结果。把 `origin` 修正为自己的仓库后再尝试。

如果 `origin` 已经属于自己，再检查 GitHub Desktop 当前登录账号是否拥有该仓库，以及系统保存的 GitHub 凭据是否属于同一账号。不要通过索要他人的 Token 解决权限问题。

## 向 AI 反馈 Fork 或克隆问题的模板

```text
我的目标是：把自己的 Firefly Fork 克隆到 Windows 本地。
官方仓库地址是：
我的 Fork 地址是：
GitHub Desktop 登录账号是：
选择的本地路径是：
当前终端目录是：
git remote -v 输出是：
git status 输出是：
我执行的动作是：
完整错误是：
我预期看到的是：

请先判断问题发生在 Fork、账号、克隆、目录、远程地址还是权限，
然后只给出一个最小检查步骤和验收标准。
```

发送前删除 Token、验证码和不需要公开的私人路径。GitHub 用户名和公开仓库地址可以帮助判断仓库归属，但密码和访问令牌永远不应放进聊天记录。

完成本篇后，下一步可以继续学习项目结构和配置修改：[认识 Firefly 文件结构并修改配置](/posts/setup-project-foundation/)。
