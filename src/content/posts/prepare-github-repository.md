---
title: "把代码放到 GitHub：为部署准备远程仓库"
published: 2026-07-03T16:25:58+08:00
updated: 2026-07-03T16:25:58+08:00
pinned: false
description: "上一篇我们先讲清楚了“部署到底是在做什么”。"
tags: ["AI编程", "ai编程助手", "ai IDE/编辑器", "版本控制", "部署发布"]
category: "发布篇"
draft: false
---

> 发布不是从部署平台开始的。
> 对一个前端项目来说，更稳的第一步，是先把本地代码整理好，提交到 Git，并推送到 GitHub。

---

上一篇我们先讲清楚了“部署到底是在做什么”。

本地地址 `http://127.0.0.1:5177/` 只能给自己看。
真正的发布，是让项目通过公网链接被别人访问。

但在 GitHub Pages、Vercel 或 Netlify 能读取项目之前，我们需要先完成一件事：

> **把代码放到一个远程仓库里。**

这篇文章不急着点部署按钮。
我们先把“远程仓库准备”这一步拆清楚。

---

## 为什么发布前要先有 GitHub 仓库

现在代码在你的电脑里。
部署平台看不到你的本地文件夹。

它需要一个可以访问的代码来源。
最常见的代码来源就是 GitHub 仓库。

可以把关系理解成这样：

| 位置 | 作用 |
|------|------|
| 本地文件夹 | 你写代码、改文章、调样式的地方 |
| 本地 Git 仓库 | 记录每一次改动的地方 |
| GitHub 远程仓库 | 让部署平台和其他人读取代码的地方 |
| 部署平台 | 拉取代码、运行构建、生成线上网站的地方 |

所以 GitHub 不是部署本身。

它更像发布流程里的“代码中转站”和“版本记录中心”。

---

## 先确认本地项目是一个 Git 仓库

在项目文件夹里运行：

```bash
git status
```

如果它能显示当前分支和文件状态，说明这里已经是一个 Git 仓库。

如果看到类似下面的提示：

```text
fatal: not a git repository
```

说明还没有初始化 Git。

这时可以运行：

```bash
git init
```

不过本站前面已经做过项目基础，所以这里更常见的情况是：

> Git 已经存在，只是有很多文件还没提交。

---

## 发布前先让 git status 变得可读

准备推送到 GitHub 之前，最重要的不是马上提交。

而是先看清楚当前工作区。

运行：

```bash
git status --short
```

你会看到类似：

```text
 M src/App.tsx
 M src/data/articles.ts
?? src/content/articles/prepare-github-repository.md
```

这里的含义是：

| 标记 | 含义 |
|------|------|
| `M` | 已跟踪文件被修改 |
| `??` | 新文件，还没有被 Git 跟踪 |

如果你是和 AI 一起写项目，这一步尤其重要。

因为 AI 可能一次改了多个文件。
你需要知道：

1. 哪些文件是这次任务真正需要的
2. 哪些文件是之前遗留的改动
3. 哪些文件不应该被提交
4. 有没有生成物、临时文件、截图草稿混在里面

发布前的清醒，很多时候就来自 `git status`。

---

## 检查哪些文件应该提交

一个前端项目通常应该提交这些内容：

- `src` 里的源码
- `public` 里的静态资源
- `package.json`
- `package-lock.json`
- `index.html`
- `vite.config.ts`
- `tsconfig` 相关配置
- `README.md` 或项目文档

通常不应该提交这些内容：

- `node_modules`
- `dist`
- `.env` 里的敏感配置
- 临时截图
- 系统缓存文件
- 编辑器自动生成的无关文件

为什么 `dist` 通常不提交？

因为部署平台可以自己运行：

```bash
npm run build
```

然后生成新的 `dist`。

源码才是项目的根。
构建产物只是源码生成出来的结果。

---

## 确认 .gitignore 是否够用

在推送到 GitHub 前，要看一下 `.gitignore`。

一个 Vite 项目通常至少需要忽略：

```txt
node_modules
dist
.env
.env.local
```

`node_modules` 特别不能提交。

它体积很大，而且别人可以通过：

```bash
npm install
```

根据 `package.json` 和 `package-lock.json` 重新安装依赖。

如果你把 `node_modules` 提交到 GitHub，仓库会变得很臃肿，也容易引入跨系统问题。

---

## 提交前先跑一次构建

在准备提交之前，建议先运行：

```bash
npm run build
```

这一步不是形式主义。

它能帮你确认：

1. TypeScript 没有明显类型错误
2. Markdown 文章能被正确导入
3. 资源路径没有在构建阶段出错
4. 项目至少能生成可部署产物

如果构建失败，不要先提交。

先回到调试篇的方法：

- 看第一条真正的报错
- 判断是语法问题、导入问题，还是资源路径问题
- 修好后重新构建
- 再考虑提交

一个好习惯是：

> 每次准备发布前，都让本地构建先通过。

---

## 添加文件到暂存区

确认文件没问题后，就可以把这次要提交的内容加入暂存区。

如果你确定所有改动都属于这次任务，可以运行：

```bash
git add .
```

但在学习阶段，我更建议先精确一点：

```bash
git add src/content/articles/prepare-github-repository.md
git add src/data/articles.ts
```

这样做的好处是：

> 你知道自己这次提交到底包含了什么。

等你更熟悉 Git 之后，再根据情况使用 `git add .`。

---

## 写一个能看懂的 commit message

提交命令是：

```bash
git commit -m "docs: add GitHub repository preparation article"
```

提交信息不需要写成作文。
但它应该让未来的你看得懂。

可以用这种格式：

```text
类型: 做了什么
```

常见类型：

| 类型 | 适合场景 |
|------|----------|
| `feat` | 新功能 |
| `fix` | 修 Bug |
| `docs` | 文档或文章 |
| `style` | 样式调整 |
| `refactor` | 重构 |
| `chore` | 工程配置或杂项 |

本站新增文章时，可以用：

```bash
git commit -m "docs: add publishing workflow article"
```

如果同时改了页面功能，也可以用 `feat`。

关键不是格式多高级，而是：

> 让每一次提交都像一个清楚的小存档。

---

## 在 GitHub 创建远程仓库

接下来去 GitHub 创建一个新仓库。

需要注意几个选项：

1. 仓库名尽量简单，比如 `ai-learning-site`
2. 如果本地已经有 README，不要在 GitHub 上重复初始化 README
3. 可以先选择 Public，方便后续部署平台读取
4. 不要勾选自动添加 `.gitignore`，本地已有的话会更清楚

创建完成后，GitHub 会给你一个仓库地址。

通常长这样：

```text
https://github.com/your-name/ai-learning-site.git
```

这个地址就是远程仓库地址。

---

## 连接本地仓库和 GitHub

回到本地终端，运行：

```bash
git remote add origin https://github.com/your-name/ai-learning-site.git
```

这里的 `origin` 是远程仓库的默认名字。

添加后可以检查：

```bash
git remote -v
```

你应该能看到类似：

```text
origin  https://github.com/your-name/ai-learning-site.git (fetch)
origin  https://github.com/your-name/ai-learning-site.git (push)
```

这表示：

> 本地仓库已经知道要把代码推送到哪里。

---

## 推送代码到 GitHub

如果当前分支叫 `main`，可以运行：

```bash
git push -u origin main
```

如果当前分支叫 `master`，可以运行：

```bash
git push -u origin master
```

怎么知道当前分支叫什么？

运行：

```bash
git branch --show-current
```

第一次推送时加 `-u`，是为了建立本地分支和远程分支的关联。

以后再推送，通常只需要：

```bash
git push
```

---

## 推送失败时不要慌

第一次推送很容易遇到问题。

常见情况有三类。

### 1. 没有登录或没有权限

可能会看到认证失败。

这通常不是代码问题，而是 GitHub 登录方式的问题。

可以检查：

- GitHub 账号是否正确
- 是否使用了有效的 token
- 当前仓库是否属于你的账号
- HTTPS / SSH 方式是否配置正确

### 2. 远程仓库不是空的

如果你在 GitHub 创建仓库时勾选了 README，本地和远程可能都有不同的初始提交。

这时需要先处理远程历史。

学习阶段更简单的做法是：

> 创建空仓库，不在 GitHub 页面上额外生成 README。

### 3. 分支名不一致

本地可能是 `master`，GitHub 默认提示可能是 `main`。

先运行：

```bash
git branch --show-current
```

确认本地分支名，再决定推送命令。

---

## 让 AI 帮你检查远程仓库准备情况

你可以这样问 AI：

```text
请帮我检查这个项目是否已经准备好推送到 GitHub。
请依次检查：
1. git status 是否清晰
2. .gitignore 是否排除了 node_modules、dist、env 文件
3. npm run build 是否通过
4. 哪些文件应该加入本次提交
5. commit message 应该怎么写
6. 当前分支名是什么
7. 是否已经配置 remote origin
8. 如果要推送到 GitHub，下一条命令应该是什么

请先解释检查结果，不要直接执行 push。
```

最后一句很重要。

在你还不熟悉发布流程时，先让 AI 给出检查结果。
确认没问题后，再执行推送。

---

## 本站这一步对应什么

回到本站。

现在我们已经有了：

- PRD
- 项目基础
- 首页
- 文章阅读页
- 多篇学习文章
- 调试记录
- 发布前理解

如果要正式部署，下一步就是：

1. 确认所有文章都已登记
2. 确认首页和文章页能正常打开
3. 确认构建通过
4. 提交当前代码
5. 推送到 GitHub
6. 让部署平台读取 GitHub 仓库

这也是为什么“远程仓库准备”不是一个额外步骤。

它是从本地项目走向线上项目的桥。

---

## 小结

这篇文章完成的是发布篇的第二步：准备 GitHub 远程仓库。

你需要记住：

1. 部署平台通常从 GitHub 读取代码
2. 推送前先用 `git status` 看清楚工作区
3. `.gitignore` 要排除 `node_modules`、`dist` 和敏感配置
4. 提交前先运行 `npm run build`
5. commit message 要能说明这次改动
6. GitHub 新仓库尽量保持空仓库，避免初始历史冲突
7. `remote origin` 是本地仓库和 GitHub 仓库之间的连接
8. 第一次推送要注意当前分支名是 `main` 还是 `master`

发布不是“把文件扔上网”。

它更像一条有顺序的流水线：

> 本地代码 → Git 提交 → GitHub 仓库 → 部署平台 → 线上链接

下一篇，我们就可以继续往前走：选择一个部署平台，并把这个静态网站真正发布出去。

---

*从零开始学AI编程 · 阶段六 · 发布篇*
