---
title: "提交并推送到 GitHub"
published: 2026-07-12T20:00:00+08:00
updated: 2026-07-13T15:30:00+08:00
pinned: false
description: "在 Firefly 中检查改动、运行验证、创建本地提交，并把 master 分支推送到自己的 GitHub 仓库。"
tags: [AI编程, Firefly, Git, GitHub, 版本控制]
category: 博客指南
draft: false
---

完成 [新增一篇 Markdown 文章](/posts/write-first-code-with-ai/) 后，文章还只存在于自己的电脑上。要让改动进入自己的 GitHub 仓库，需要经过两个动作：先在本地创建 commit，再把 commit push 到远程仓库。

不要把“保存文件”“提交”和“推送”混为一谈。它们分别对应工作区、本地 Git 历史和 GitHub 远程仓库。

## 先理解 Git 的四个位置

Git 工作流可以理解为文件在四个位置之间移动：

```text
工作区
  ↓ git add
暂存区
  ↓ git commit
本地仓库
  ↓ git push
GitHub 远程仓库
```

### 工作区

工作区是文件管理器和 VS Code 中看到的项目目录。保存文件只会改变工作区，不会自动生成 Git 版本。

### 暂存区

暂存区是一份“下一个 commit 准备包含什么”的清单。`git add` 不是上传，也不是永久保存，它只是选择内容。

### 本地仓库

`git commit` 把暂存区记录为一个带有作者、时间、说明和唯一哈希的本地版本。即使没有网络，也可以创建 commit。

### 远程仓库

`git push` 把本地已有的 commit 发送到 GitHub。它不会自动包含仍停留在工作区、尚未提交的修改。

这解释了几个常见现象：

- 保存了文件，但 `git log` 没变化：因为还没有 commit。
- commit 成功，但 GitHub 看不到：因为还没有 push。
- push 成功，但某个文件仍没上去：它可能没有进入暂存区或 commit。
- GitHub 已有 commit，但线上网站没更新：部署是 push 之后的独立流程。

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

## 提交前先定义范围

一次提交应该表达一个完整而集中的变化。例如：

- 扩写一篇教程；
- 修复一个导航链接；
- 更新一组属于同一文章的配图；
- 调整一项站点配置并补充相关类型。

不要把“新增文章、升级依赖、修改主题颜色、删除临时文件”混进一个 commit。范围过大时，审查、回退和定位问题都会变困难。

开始前写一张提交卡片：

```text
本次目标：
计划包含的文件：
明确不包含的文件：
验证命令：
提交类型：docs / feat / fix / chore
目标分支：
目标远程：
```

如果 `git status` 中出现卡片之外的文件，先保留它们在工作区，只暂存本次范围内的文件。

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

## 操作前的身份与安全检查

### 检查 Git 提交身份

Git 提交作者来自 Git 配置，不一定与 GitHub Desktop 当前登录账号相同：

```bash
git config user.name
git config user.email
```

如果仓库级配置没有输出，可以查看全局配置：

```bash
git config --global user.name
git config --global user.email
```

确认姓名和邮箱是准备显示在提交历史中的身份。不要照抄别人的邮箱，也不要为了通过检查随意填写不存在的信息。如果希望隐藏真实邮箱，可以使用 GitHub 提供的隐私邮箱，并在 GitHub 账号中确认设置。

### 检查远程仓库归属

`origin` 应指向自己的 Fork，而不是 `CuteLeaf/Firefly` 官方仓库。公开仓库允许读取不代表允许推送。检查完整的所有者与仓库名，不要只看末尾的 `Firefly.git`。

### 检查敏感信息

提交前查看差异中是否包含：

- API Key、Token、密码和验证码；
- `.env` 或其他本地环境配置；
- 真实邮箱、私人目录或内部网址；
- 浏览器 Cookie、本地存储和登录截图；
- 调试日志中的认证头或查询参数；
- 私钥、证书和云平台凭据。

如果秘密已经进入 commit，仅仅再创建一个“删除秘密”的 commit 并不能从历史中移除它。应立即撤销或轮换凭据，再根据是否已经推送选择安全的历史修复方案。不要把真实秘密发给 AI 寻求帮助。

## 具体怎么做

### 1. 进入项目根目录并查看状态

在 VS Code 终端或 PowerShell 中执行：

~~~bash
git status --short --branch
git remote -v
git branch --show-current
~~~

第一条命令确认当前分支和文件改动，第二条命令确认 `origin` 指向自己的仓库，第三条命令确认分支名称。

还可以确认项目根目录和跟踪关系：

~~~bash
git rev-parse --show-toplevel
git status
~~~

完整 `git status` 会用自然语言说明当前分支是否领先或落后远程、哪些文件已暂存、哪些文件尚未暂存，以及是否存在未跟踪文件。

### 读懂 `git status --short`

短格式每行开头有两个位置，分别代表暂存区和工作区状态：

| 示例 | 含义 |
| --- | --- |
| ` M file.md` | 文件在工作区修改，尚未暂存 |
| `M  file.md` | 修改已经进入暂存区 |
| `MM file.md` | 已暂存一次，之后工作区又继续修改 |
| `?? image.png` | 新文件尚未跟踪 |
| ` D file.md` | 工作区删除了已跟踪文件，尚未暂存 |
| `D  file.md` | 删除操作已经暂存 |

提交前不要只数文件数量，要理解每个状态。尤其是 `MM`：commit 只会包含已暂存版本，后续编辑仍留在工作区。

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

### 怎样阅读差异

`git diff --stat` 只显示文件和行数摘要，不能代替正文审查。继续使用：

~~~bash
git diff -- src/content/posts/文章文件.md
~~~

差异中以 `-` 开头的是删除内容，以 `+` 开头的是新增内容。重点检查：

- 是否误删了段落、frontmatter 或结尾标记；
- 日期、slug、内部链接和路径是否正确；
- 是否出现乱码或异常换行；
- 格式化工具是否改写了整个文件；
- 图片或新文件是否仍处于未跟踪状态；
- 生成文件是否因为运行构建而意外变化。

对新建且尚未跟踪的文件，普通 `git diff` 不显示完整内容。需要在编辑器中阅读文件，并结合 `git status --short` 确认它存在。

### 3. 提交前运行项目验证

文章或配置修改完成后，先执行：

~~~bash
pnpm check
pnpm build
~~~

`pnpm check` 检查 Astro 内容和项目诊断，`pnpm build` 检查静态页面是否可以生成。任一命令失败，都先修复问题，再进入提交步骤。

如果是视觉或交互改动，还应运行开发服务器并检查实际页面。命令通过只能证明结构和构建满足规则，不能证明文案、图片和布局符合预期。

记录验证结果：

~~~text
pnpm check：通过 / 失败
pnpm build：通过 / 失败
浏览器页面：已检查 / 未检查
检查时间：
~~~

不要在提交信息中写“tested”却没有真正执行验证。

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

如果误把一个文件加入暂存区，但想保留工作区修改，可以取消暂存：

~~~bash
git restore --staged 路径/文件名
~~~

这个命令只把文件从暂存区移回工作区，不会删除你的编辑内容。取消暂存后再次运行 `git status --short` 确认状态。

不要用 `git reset --hard` 清理暂存区，它会同时丢弃工作区修改，风险远大于当前需求。

### 5. 检查暂存区

加入暂存区后，检查“真正会进入 commit 的内容”：

~~~bash
git diff --cached --stat
git diff --cached --check
git status --short
~~~

`git diff` 查看尚未暂存的改动，`git diff --cached` 查看已经暂存的改动。提交前必须确认后者只包含这次任务的内容。

还要检查是否存在“暂存后又修改”的文件：

~~~bash
git diff --stat
git diff --cached --stat
~~~

如果同一个文件同时出现在两边，说明暂存区版本和工作区版本不同。决定本次 commit 应包含哪一版，再重新暂存或保留后续修改，不要在不知情时提交旧内容。

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

一条好的提交信息通常满足：

- 使用动词说明完成的变化；
- 与暂存区实际范围一致；
- 不写“update”“修改一下”这类缺少对象的信息；
- 不把文件列表当作提交目的；
- 不声称实现了尚未完成或未验证的功能。

例如：

~~~text
docs: expand local Firefly setup guide
fix: correct article image path
feat: add author profile link
chore: update generated icon constants
~~~

提交完成后确认本地历史：

~~~bash
git log -1 --oneline
git status --short --branch
~~~

此时 commit 已经在本地，但还没有进入 GitHub。

如果 `git commit` 提示没有可提交内容，先查看状态：可能文件尚未暂存、修改已经被之前的 commit 包含，或者当前工作区确实没有变化。不要为了制造 commit 随意修改空白字符。

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

推送前可以先查看分支与远程的关系：

~~~bash
git status --short --branch
git branch -vv
~~~

如果状态显示本地落后远程，先获取并理解远程变化，不要直接强制推送。如果本地领先远程一个或多个 commit，则这些 commit 会在 push 时上传。

正常个人 Fork 流程不需要 `--force`。强制推送会改写远程历史，可能覆盖其他设备或协作者的提交。

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

本地与远程 commit 可以通过完整哈希对比：

~~~bash
git rev-parse HEAD
git ls-remote origin refs/heads/master
~~~

两处哈希一致，表示远程 `master` 已指向当前本地提交。分支名要替换成自己的实际分支。

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

使用图形界面同样要注意：

- 仓库名称相同不代表本地目录相同，应核对 Repository settings 中的 Local path；
- 文件左侧复选框决定是否进入下一次提交；
- 提交后仍需点击 `Push origin`；
- History 中显示提交不代表它已经到达 GitHub；
- 推送前如果出现 Fetch 或 Pull 提示，应先理解远程变化。

不要在 GitHub Desktop 和终端中同时执行相互冲突的提交或分支操作。可以混合使用，但每一步后都要刷新并重新查看状态。

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

### 保存提交与推送记录

~~~text
任务范围：
变更文件：
当前分支：
origin：
检查结果：
提交哈希：
提交信息：
推送结果：
GitHub 最新提交：
仍留在工作区的其他修改：
~~~

如果工作区还有其他任务的修改，推送完成后仍然显示非空状态是正常的。关键是本次范围已经进入 commit，其他修改没有被夹带。

## 失败时先检查什么

### `fatal: not a git repository`

当前终端不在 Firefly 项目根目录。执行 `Get-Location`，再进入包含 `.git` 和 `package.json` 的目录：

~~~powershell
Set-Location "E:\Users\13700HX\Documents\GitHub\Firefly"
~~~

然后重新执行 `git status`。

如果电脑中有多个 Firefly 目录，使用 `git rev-parse --show-toplevel` 和 `git remote -v` 确认当前目录既是正确仓库，又连接到自己的 Fork。不要只根据文件夹名称判断。

### Git 提示没有配置作者身份

错误通常会要求设置 `user.name` 和 `user.email`。先决定配置只适用于当前仓库还是全局所有仓库：

~~~bash
git config user.name "你的提交显示名称"
git config user.email "你的 GitHub 邮箱或隐私邮箱"
~~~

上面是不带 `--global` 的仓库级配置，只影响当前项目。配置后再次读取确认，再重新提交。不要复制教程作者的姓名和邮箱。

### `nothing to commit`

先执行：

~~~bash
git status --short
git diff
git diff --cached
~~~

可能原因包括：文件没有保存、修改与当前版本相同、内容尚未暂存，或者已经被前一个 commit 包含。不要为了让 commit 命令成功而创建无意义改动。

### 新文件没有出现在普通 diff 中

未跟踪文件会显示为 `??`，但普通 `git diff` 不展示它的完整正文。先在编辑器中审阅，再使用明确路径 `git add`，随后用 `git diff --cached` 检查真正将要提交的内容。

### 暂存区包含错误文件

使用：

~~~bash
git restore --staged 路径/错误文件
~~~

取消暂存后文件修改仍会保留。再次检查 `git status --short` 与 `git diff --cached --stat`，确认提交范围已经缩小。

### `git push` 提示没有权限或认证失败

先执行 `git remote -v`，确认远程地址是自己的仓库。再确认 GitHub Desktop 已登录正确账号，或终端使用的 GitHub 凭据有该仓库的写入权限。不要把密码、Token 或私钥写进文章或提交内容。

把“认证失败”和“仓库权限不足”分开：认证失败表示 GitHub 无法确认你的身份，权限不足表示身份已确认但该账号不能写入目标仓库。远程指向官方仓库时，即使成功登录自己的账号也通常没有推送权限。

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

远程多出的提交可能来自 GitHub 网页编辑、另一台电脑或 GitHub Desktop。先查看图形历史，理解双方修改是否冲突。涉及合并或变基时，把它作为独立任务处理，不要在不清楚状态时连续尝试多个命令。

### 当前处于 detached HEAD

`git status` 如果提示不在任何分支上，不要直接提交并推送到猜测的分支。先记录当前提交：

~~~bash
git log -1 --oneline
git branch --show-current
~~~

确认为什么检出了某个历史提交，再决定创建分支还是返回原分支。切换前先保护工作区修改，避免丢失未提交内容。

### commit 后发现漏了文件

如果还没有推送，可以补充文件后执行：

~~~bash
git add <漏掉的文件>
git commit --amend --no-edit
~~~

如果已经推送，先不要重写公共历史。创建一个新的修复 commit，再正常推送。

如果尚未推送但当前工作区还混有其他修改，也不要直接 `git add .` 后 amend。只暂存漏掉的明确文件，并用 `git diff --cached` 确认 amend 范围。

### commit 中出现敏感信息

立即停止推送。如果秘密是真实凭据，先到对应平台撤销或轮换它，即使还没有推送也不应继续使用。然后确认敏感内容出现在哪个文件和提交中，再选择适合“尚未推送”或“已经推送”的历史清理方案。

不要仅在新 commit 中删除秘密后认为安全，也不要把包含秘密的 diff、日志或截图发给 AI。

### 推送成功但 GitHub 页面没有更新

先确认查看的是正确仓库和正确分支，再用 `git log -1 --oneline` 与 GitHub 的最新 commit 对比。代码到达 GitHub 不等于 GitHub Pages 已经部署完成，后续还要检查 Actions 和 Pages 状态。

浏览器页面可能停留在旧分支或旧缓存。先检查 GitHub 页面上的分支选择器和最新提交哈希，再刷新页面。只要远程分支哈希与本地 `HEAD` 一致，推送本身已经完成。

## 向 AI 反馈 Git 提交或推送问题的模板

~~~text
我的目标是：把本次 Firefly 修改提交并推送到自己的 GitHub 仓库。
项目根目录是：
本次计划提交的文件是：
当前分支是：
git status --short --branch 输出是：
git remote -v 输出是：
git log -1 --oneline 输出是：
我执行的命令是：
完整错误是：
是否已经推送：是 / 否
是否包含敏感信息：是 / 否 / 不确定
我预期看到的是：

请先判断问题属于目录、工作区、暂存区、提交、分支、远程、认证还是推送，
然后只给出一个不会丢失现有修改的最小检查步骤和验收标准。
~~~

发送前移除 Token、密码、邮箱中的隐私部分和私有仓库信息。可以提供提交哈希、公开仓库地址和经过脱敏的错误，但永远不要提供凭据本身。

---

*从零开始学 AI 编程 · 阶段五 · Firefly 实战*
