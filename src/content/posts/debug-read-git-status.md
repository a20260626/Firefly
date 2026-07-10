---
title: "改了很多文件后别乱提交：读懂 git status"
published: 2026-07-03T16:15:18+08:00
updated: 2026-07-03T16:15:18+08:00
pinned: false
description: "上一篇我们讲了样式类调试。"
tags: ["AI编程", "ai编程助手", "ai IDE/编辑器", "版本控制"]
category: "调试篇"
draft: false
---

> 调试不只发生在页面和代码里，也发生在 Git 工作区里。
> 当你改了很多文件、加了很多文章，不知道哪些该提交时，第一步不是慌，而是读懂 `git status`。

---

上一篇我们讲了样式类调试。

页面没有报错，但布局被表格、代码块、图片或长链接撑坏时，要从元素宽度和 CSS 约束开始排查。

这一篇换一个地方：

> **Git 工作区。**

很多新手第一次看到 `git status` 会紧张。

尤其是 AI 帮你做了很多修改以后，终端里出现一长串：

```text
 M src/App.tsx
 M src/data/articles.ts
?? src/content/articles/new-article.md
?? public/assets/example.svg
```

看起来像一堆警告。

其实它只是在告诉你：

> 哪些文件改了，哪些文件是新加的，哪些文件还没有被 Git 跟踪。

读懂它，你就知道提交前应该整理什么。

---

## git status 是什么

`git status` 是 Git 里最常用的检查命令之一。

它不会修改文件。

它只是告诉你当前工作区状态。

常用命令是：

```bash
git status --short --branch
```

它会用比较短的格式显示：

- 当前在哪个分支
- 哪些文件被修改
- 哪些文件是新文件
- 哪些文件已经暂存
- 哪些文件还没被跟踪

对新手来说，先记住一句话：

> **提交前先看 status。**

不要在不知道改了什么的情况下提交。

---

## 最常见的几个符号

`git status --short` 里最常见的是这几个符号。

| 符号 | 意思 |
|------|------|
| `M` | modified，文件被修改了 |
| `??` | untracked，新文件，Git 还没跟踪 |
| `A` | added，新文件已经加入暂存区 |
| `D` | deleted，文件被删除 |
| `R` | renamed，文件被重命名 |

比如：

```text
 M src/data/articles.ts
?? src/content/articles/debug-read-git-status.md
```

意思是：

- `src/data/articles.ts` 是已有文件，被修改了
- `debug-read-git-status.md` 是新文件，还没有被 Git 跟踪

这不是错误。

这是 Git 在告诉你当前变化。

---

## M 和 ?? 的区别

新增文章时，你经常会同时看到 `M` 和 `??`。

比如：

```text
 M src/data/articles.ts
?? src/content/articles/debug-read-git-status.md
```

这是正常的。

因为新增文章通常需要做两件事：

1. 创建新的 Markdown 文件
2. 修改 `articles.ts` 注册这篇文章

所以 Git 会看到：

- 一个老文件被修改：`M src/data/articles.ts`
- 一个新文件出现：`?? src/content/articles/debug-read-git-status.md`

这两个要一起看。

如果只提交了 `articles.ts`，但忘记提交 Markdown 文件，项目构建时可能找不到文章文件。

如果只提交了 Markdown 文件，但忘记提交 `articles.ts`，页面里可能看不到这篇文章。

所以新增文章时，通常这两个变化要成对提交。

---

## 为什么会有很多未跟踪文件

当前项目里已经有很多文章和素材。

如果这些文件还没被提交过，`git status` 会显示很多 `??`。

例如：

```text
?? src/content/articles/debug-with-ai-first-time.md
?? src/content/articles/debug-new-article-build-failure.md
?? src/content/articles/debug-layout-overflow.md
?? public/assets/m1-build-success.svg
```

这表示：

> 这些文件存在于文件夹里，但 Git 还没有把它们纳入版本历史。

这不一定是坏事。

它只是提醒你：

- 这些文件还没提交
- 如果你现在换机器或重置目录，它们可能不会被 Git 保存
- 提交前要判断它们是不是应该进入版本库

对于本站来说，文章 Markdown 和公开素材通常应该提交。

但根目录里的临时源文件、截图草稿、未整理素材，就要先判断清楚。

---

## 不要盲目 git add .

很多教程会告诉你：

```bash
git add .
git commit -m "update"
```

这对小练习可以。

但在真实项目里要谨慎。

因为 `git add .` 会把当前目录下所有变化都加入暂存区。

如果工作区里有：

- 临时文件
- 用户给的源文件
- 不想提交的截图
- 调试中间产物
- 其他未完成改动

它们也可能被一起加进去。

更稳的做法是：

```bash
git status --short --branch
```

先看清楚，再按文件添加。

比如新增这篇文章时，更明确的做法是：

```bash
git add src/data/articles.ts src/content/articles/debug-read-git-status.md
```

这表示：

> 我知道这次提交要包含哪两个文件。

---

## 提交前先看 diff

`git status` 告诉你哪些文件变了。

但它不告诉你具体改了什么。

要看具体内容，可以用：

```bash
git diff
```

如果只想看某个文件：

```bash
git diff src/data/articles.ts
```

新增文章时，可以重点看 `articles.ts`：

- 有没有导入新 Markdown
- import 名称是否正确
- `articleSources` 里是否注册了 slug
- 注册顺序是否符合文章顺序

比如你应该能看到类似：

```diff
+import debugReadGitStatusContent from '../content/articles/debug-read-git-status.md?raw';

+  {
+    slug: 'debug-read-git-status',
+    raw: debugReadGitStatusContent,
+  },
```

diff 是提交前的最后复查。

不要只看文件名。

也要看改动内容是否符合预期。

---

## staged 和 unstaged 是什么

Git 里有一个概念叫暂存区。

你可以把它理解成：

> 下一次提交准备打包的文件清单。

工作区里改了文件，不代表它一定会进入下一次提交。

你需要先 `git add`。

比如：

```bash
git add src/data/articles.ts
```

这时 `articles.ts` 就进入暂存区。

如果再运行：

```bash
git status
```

Git 会把变化分成两类：

- Changes to be committed：已经暂存，下一次 commit 会包含
- Changes not staged for commit：还没暂存，下一次 commit 不包含

新手最常见的问题是：

> 以为文件改了就会自动提交。

不会。

Git 只会提交已经暂存的内容。

---

## 新增文章时应该检查哪些文件

对于这个项目，新增一篇文章通常至少涉及两个文件：

| 文件 | 为什么要提交 |
|------|--------------|
| `src/content/articles/xxx.md` | 新文章正文 |
| `src/data/articles.ts` | 文章注册表 |

如果文章里引用了新图片，还要提交：

| 文件 | 为什么要提交 |
|------|--------------|
| `public/assets/xxx.svg` 或图片 | 文章引用的素材 |

如果只是写文章，没有改样式或组件，一般不应该出现：

- `src/App.tsx`
- `src/styles.css`
- `src/components/ArticleReader.tsx`

当然，当前项目里可能已有未提交改动。

这时要看这些文件是不是之前功能留下的变化。

不要因为这次只是写文章，就随手把所有变化都提交进去。

提交要讲清楚主题。

---

## 如何让 AI 帮你读 git status

你可以把 `git status --short --branch` 的输出给 AI。

然后这样问：

```text
这是当前 git status：
[粘贴输出]

我这次只想提交刚新增的文章。
请帮我判断：
1. 哪些文件和这次新增文章有关
2. 哪些文件看起来是之前遗留改动
3. 应该 git add 哪些文件
4. 不要让我执行 git add .，请给出明确文件列表
```

这个提示词很好用。

它会让 AI 帮你区分：

- 当前任务相关文件
- 之前未提交的文件
- 不应该混进这次提交的文件

这比直接说"帮我提交"安全。

---

## 不要随便删除不认识的文件

看到很多 `??` 时，新手可能会想：

> 这些是不是垃圾文件？要不要删掉？

不要急。

`??` 只表示 Git 没跟踪。

它不等于没用。

比如本站里的源文章文件：

```text
?? stage1-cognition.md
?? stage2-setup.md
?? stage3-dialogue.md
```

它们可能是用户提供的原始素材。

即使不提交，也不应该随便删除。

调试 Git 状态时，一个重要原则是：

> 不认识的文件先保留，先问清楚用途。

尤其是别人放进项目里的文件，更不要为了让 status 干净就删除。

---

## 提交信息怎么写

提交信息不用很长，但要说明这次做了什么。

比如新增一篇调试文章，可以写：

```text
docs: add git status debugging article
```

或者如果这次新增了一组调试篇文章：

```text
docs: expand debugging stage articles
```

如果这次既改了功能又改了文章，最好拆成不同提交。

比如：

1. 功能提交：`feat: add article navigation`
2. 内容提交：`docs: add debugging articles`

这样以后回看历史会更清楚。

提交信息的目标不是华丽。

而是让未来的你知道：

> 这次提交到底保存了什么。

---

## 一个安全提交流程

新增文章后的安全流程可以这样：

```bash
git status --short --branch
git diff src/data/articles.ts
npm run build
git add src/data/articles.ts src/content/articles/debug-read-git-status.md
git status --short --branch
git commit -m "docs: add git status debugging article"
```

这个顺序有几个好处：

1. 先看当前状态
2. 再看关键 diff
3. 构建通过后再准备提交
4. 明确添加相关文件
5. 提交前再看一次状态
6. 最后提交

这就是比较稳的 Git 节奏。

慢一点，但不容易把工作区搞乱。

---

## 常见问题

### 1. `M` 是不是错误？

不是。

`M` 只是表示文件被修改。

你要判断的是这次修改是不是你想要的。

### 2. `??` 是不是垃圾文件？

不是。

`??` 只是表示 Git 还没有跟踪这个文件。

它可能是新文章，也可能是临时文件。

先判断用途，再决定是否提交。

### 3. 为什么不要直接 `git add .`？

因为它会把所有变化都加入暂存区。

如果工作区里有不相关改动，就会混进提交。

明确文件列表更安全。

---

## 小结

这一篇练习的是 Git 状态调试。

你需要记住这条线：

1. 提交前先运行 `git status --short --branch`
2. `M` 表示已有文件被修改
3. `??` 表示新文件还没被 Git 跟踪
4. 新增文章通常要同时提交 Markdown 文件和 `articles.ts`
5. 不要盲目 `git add .`
6. 提交前用 `git diff` 看关键改动
7. 不认识的未跟踪文件不要随便删
8. 提交信息要说明这次保存了什么

Git 不是只在最后提交时才出现。

它是你和 AI 协作时的安全网。

> **读懂 git status，你就知道项目现在发生了什么。**

下一篇，我们可以继续看调试篇的收尾：如何把一次报错修复整理成可复用的经验，避免下次重复踩同一个坑。

---

*从零开始学AI编程 · 阶段五 · 调试篇*
