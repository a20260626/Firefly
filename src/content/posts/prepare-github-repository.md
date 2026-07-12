---
title: "提交并推送到 GitHub"
published: 2026-07-12T20:00:00+08:00
updated: 2026-07-12T20:00:00+08:00
pinned: false
description: "在 Firefly 中检查改动、运行验证、创建本地提交，并把 master 分支推送到自己的 GitHub 仓库。"
tags: [AI编程, Firefly, Git, GitHub, 版本控制]
category: 博客指南
draft: false
---

完成 [新增一篇 Markdown 文章](/posts/write-first-code-with-ai/) 后，文章还只存在于自己的电脑上。要让改动进入自己的 GitHub 仓库，需要经过两个动作：先在本地创建 commit，再把 commit push 到远程仓库。

不要把“保存文件”“提交”和“推送”混为一谈。它们分别对应工作区、本地 Git 历史和 GitHub 远程仓库。

## 问题是什么

本篇只解决一个问题：**如何把确认无误的 Firefly 改动，安全地提交到本地 Git，并推送到自己的 GitHub 仓库。**

输入是本地已经完成并验证的文章或配置修改，输出是：

- 一个描述清楚的本地 commit；
- commit 已经位于当前分支；
- 当前分支已经推送到正确的 GitHub 远程仓库；
- GitHub 页面能够看到最新提交。

本篇不处理 GitHub Pages 部署，也不处理合并上游仓库。推送成功后，下一步才是让 GitHub Actions 或 GitHub Pages 使用这次代码。

先记住三层关系：

| 位置 | 代表动作 | 作用 |
| --- | --- | --- |
| 工作区 | 保存文件 | 电脑上的当前修改 |
| 暂存区和本地仓库 | `git add`、`git commit` | 准备并记录一次版本 |
| GitHub 远程仓库 | `git push` | 把本地版本上传到 GitHub |

## 适用环境是什么

本文适用于已经完成以下准备的 Windows 环境：

- 已经 Fork 并克隆了自己的 Firefly 仓库；
- 已安装 Git 或 GitHub Desktop；
- 当前终端位于包含 `package.json` 和 `.git` 的项目根目录；
- 已经完成本次文章或配置修改；
- 已经知道自己的远程仓库地址和目标分支。

当前仓库使用的典型信息是：

~~~text
分支：master
远程：origin
远程地址：https://github.com/a20260626/Firefly.git
~~~

你的用户名、仓库地址和分支名可能不同。命令中的 `origin` 和 `master` 以实际检查结果为准，不要盲目照抄。

## 具体怎么做

### 1. 进入项目根目录并查看状态

在 VS Code 终端或 PowerShell 中执行：

~~~bash
git status --short --branch
git remote -v
git branch --show-current
~~~

第一条命令确认当前分支和文件改动，第二条命令确认 `origin` 指向自己的仓库，第三条命令确认分支名称。

### 2. 确认本次应该提交哪些文件

先查看摘要和差异：

~~~bash
git diff --stat
git diff --check
git status --short
~~~

重点检查：

- 是否包含本次任务需要的文章、配置或图片；
- 是否混入了不相关的旧改动；
- 是否出现 `node_modules`、临时文件、密钥或不应提交的构建产物。

如果发现不相关的改动，不要直接执行 `git add .`。先记录文件名，再只暂存本次需要的文件。

### 3. 提交前运行项目验证

文章或配置修改完成后，先执行：

~~~bash
pnpm check
pnpm build
~~~

`pnpm check` 检查 Astro 内容和项目诊断，`pnpm build` 检查静态页面是否可以生成。任一命令失败，都先修复问题，再进入提交步骤。

### 4. 精确加入暂存区

假设这次只修改一篇文章，可以执行：

~~~bash
git add src/content/posts/write-first-code-with-ai.md
~~~

如果文章还增加了图片，再把图片单独加入：

~~~bash
git add src/content/posts/images/my-first-firefly-article.png
~~~

如果这次明确要提交多个相关文件，可以一次列出：

~~~bash
git add src/content/posts/write-first-code-with-ai.md src/content/posts/images/my-first-firefly-article.png
~~~

只有在已经确认工作区所有改动都属于同一个任务时，才考虑：

~~~bash
git add .
~~~

### 5. 检查暂存区

加入暂存区后，检查“真正会进入 commit 的内容”：

~~~bash
git diff --cached --stat
git diff --cached --check
git status --short
~~~

`git diff` 查看尚未暂存的改动，`git diff --cached` 查看已经暂存的改动。提交前必须确认后者只包含这次任务的内容。

### 6. 创建本地 commit

确认暂存区没有问题后，执行：

~~~bash
git commit -m "docs: add GitHub publishing guide"
~~~

提交信息要说明“做了什么”。常用前缀包括：

| 前缀 | 适用内容 |
| --- | --- |
| `docs:` | 文章和文档 |
| `feat:` | 新功能 |
| `fix:` | 修复问题 |
| `chore:` | 工具或维护工作 |

提交完成后确认本地历史：

~~~bash
git log -1 --oneline
git status --short --branch
~~~

此时 commit 已经在本地，但还没有进入 GitHub。

### 7. 推送到 GitHub

确认当前分支是 `master`、远程是 `origin` 后，执行：

~~~bash
git push origin master
~~~

如果这是该分支第一次推送，可以设置上游分支：

~~~bash
git push -u origin master
~~~

以后在同一分支上通常可以直接执行：

~~~bash
git push
~~~

终端出现类似 `master -> master` 的结果，通常表示推送成功。

### 8. 在 GitHub 上确认远程提交

打开 `git remote -v` 输出的仓库地址，进入当前分支的提交记录，确认最新 commit 信息与本地 `git log -1 --oneline` 一致。

也可以在终端检查远程分支：

~~~bash
git ls-remote --heads origin master
~~~

最后再次执行：

~~~bash
git status --short --branch
~~~

如果工作区没有本次遗漏的改动，且远程已经出现最新 commit，提交和推送闭环就完成了。

### 9. 使用 GitHub Desktop 完成同一流程

如果使用 GitHub Desktop，动作顺序是：

1. 打开正确的 Firefly 本地仓库；
2. 在 Changes 页面检查文件列表和差异；
3. 只勾选本次需要提交的文件；
4. 在 Summary 中填写类似 `docs: add GitHub publishing guide` 的提交信息；
5. 点击 `Commit to master`；
6. 点击顶部的 `Push origin`；
7. 打开 GitHub 仓库确认最新提交。

GitHub Desktop 只是把 `git status`、`git add`、`git commit` 和 `git push` 做成了可视化界面，版本控制的顺序没有改变。

## 如何验证完成

按下面的清单逐项确认：

- [ ] 当前终端位于 Firefly 项目根目录；
- [ ] `git status --short --branch` 显示了正确分支；
- [ ] `git remote -v` 指向自己的 GitHub 仓库；
- [ ] 已查看 `git diff --stat` 和 `git diff --check`；
- [ ] `pnpm check` 执行成功；
- [ ] `pnpm build` 执行成功；
- [ ] 暂存区只包含本次任务文件；
- [ ] `git diff --cached --check` 执行成功；
- [ ] `git commit` 成功创建本地提交；
- [ ] `git log -1 --oneline` 显示本次提交；
- [ ] `git push origin <当前分支>` 执行成功；
- [ ] GitHub 页面显示最新 commit；
- [ ] 推送后再次检查 Git 状态没有遗漏的本次改动。

推荐的命令顺序：

~~~bash
git status --short --branch
git remote -v
pnpm check
pnpm build
git diff --check
git add <本次需要提交的文件>
git diff --cached --check
git commit -m "docs: add GitHub publishing guide"
git push origin <当前分支>
git status --short --branch
~~~

完成后，本地修改才真正进入了自己的 GitHub 仓库。部署平台是否已经生成线上页面，属于下一步的 GitHub Pages 部署问题。

## 失败时先检查什么

### `fatal: not a git repository`

当前终端不在 Firefly 项目根目录。执行 `Get-Location`，再进入包含 `.git` 和 `package.json` 的目录：

~~~powershell
Set-Location "E:\Users\13700HX\Documents\GitHub\Firefly"
~~~

然后重新执行 `git status`。

### `git push` 提示没有权限或认证失败

先执行 `git remote -v`，确认远程地址是自己的仓库。再确认 GitHub Desktop 已登录正确账号，或终端使用的 GitHub 凭据有该仓库的写入权限。不要把密码、Token 或私钥写进文章或提交内容。

### `src refspec master does not match any`

可能是当前分支不是 `master`，也可能还没有创建任何 commit。先执行：

~~~bash
git branch --show-current
git log -1 --oneline
~~~

根据实际分支名推送；如果还没有 commit，先完成 `git add` 和 `git commit`。

### `rejected` 或 `non-fast-forward`

远程仓库有本地没有的提交。先不要强制推送，先查看远程变化：

~~~bash
git fetch origin
git log --oneline --graph --decorate --all -10
~~~

确认远程提交内容后，再决定是否合并或变基。除非明确知道后果，不要使用 `git push --force`。

### commit 后发现漏了文件

如果还没有推送，可以补充文件后执行：

~~~bash
git add <漏掉的文件>
git commit --amend --no-edit
~~~

如果已经推送，先不要重写公共历史。创建一个新的修复 commit，再正常推送。

### 推送成功但 GitHub 页面没有更新

先确认查看的是正确仓库和正确分支，再用 `git log -1 --oneline` 与 GitHub 的最新 commit 对比。代码到达 GitHub 不等于 GitHub Pages 已经部署完成，后续还要检查 Actions 和 Pages 状态。

---

*从零开始学 AI 编程 · 阶段五 · Firefly 实战*
