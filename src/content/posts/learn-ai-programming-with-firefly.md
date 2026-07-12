---
title: "从学 AI 编程到博客上线：一套可执行的问题解决系统"
published: 2026-07-12T10:00:00+08:00
updated: 2026-07-12T10:00:00+08:00
pinned: false
description: "以 Firefly 博客为真实项目，把学 AI 编程拆成澄清问题、安装环境、Fork 仓库、本地运行、写文章、检查构建和部署 GitHub Pages 的完整闭环。"
tags: ["AI编程", "Firefly", "问题分解", "GitHub Pages", "学习方法"]
category: "实战篇"
draft: false
---

> “学 AI 编程”不是一个可以直接执行的动作。
>
> 真正能执行的问题应该是：**我怎样借助 AI，把 Firefly 博客在本地运行起来，写入自己的文章，并部署到 GitHub Pages？**

很多人开始学习时，会先收集一长串工具和教程：ChatGPT、Node.js、Git、VS Code、GitHub……看起来准备得很充分，真正动手时却不知道下一步做什么。

问题通常不在工具，而在于：**目标还没有被拆成可以执行和验证的小步骤。**

这篇文章以搭建 Firefly 博客为真实项目，整理一套可以反复使用的问题解决系统。它不仅适用于博客，也能迁移到做饭、写作、设计、会计、驾驶，甚至任何陌生技能的学习中。

---

## 一、清晰的问题才叫问题

“学 AI 编程”更像一个方向，不是一个清晰问题。

它至少还缺少这些信息：

| 元素 | 要回答的问题 | 本项目的答案 |
|---|---|---|
| 目标 | 最终要得到什么 | 一个能在线访问的 Firefly 博客 |
| 输入 | 手里已经有什么 | Windows 电脑、GitHub 账号、Firefly 仓库 |
| 输出 | 最后要交付什么 | 本地项目、Markdown 文章、`github.io` 地址 |
| 约束 | 有什么限制 | Node.js ≥ 22、使用 pnpm、部署到 GitHub Pages |
| 依赖 | 必须先完成什么 | 安装工具、Fork 仓库、克隆代码 |
| 风险 | 哪里容易失败 | 版本不匹配、目录错误、构建失败、Pages 路径错误 |
| 验证 | 怎么证明完成 | 命令成功、页面可见、文章可打开、线上地址可访问 |

所以，开始之前先写五句话：

- [ ] 我想完成的结果是：
- [ ] 当前已知条件是：
- [ ] 我手上的输入是：
- [ ] 我希望得到的输出是：
- [ ] 完成的判断标准是：

把“我要学 AI 编程”改写成下面这句话，行动就会清晰很多：

> 我要使用 AI 辅助，在 Windows 上安装开发工具，Fork 并运行 Firefly，写入自己的 Markdown 文章，最后通过 GitHub Actions 部署到 GitHub Pages。

---

## 二、把问题拆成四层

一个大问题可以按照四层向下拆分：

1. **目标层**：我要做成什么？
2. **阶段层**：完成目标要经过哪些阶段？
3. **任务层**：每个阶段包含哪些任务？
4. **最小执行单位**：我现在只做哪个动作？

例如：

```text
搭建个人博客
└─ 准备开发环境
   └─ 安装 Node.js
      ├─ 打开 Node.js 官方下载页
      ├─ 选择 LTS 版本
      ├─ 运行安装程序
      ├─ 打开新终端
      └─ 执行 node --version 验证
```

最小执行单位最好写成：

> **动作 + 对象 + 可观察结果**

例如：

- 打开 Node.js 下载页，并确认页面有 LTS 版本。
- 在终端执行 `node --version`，并确认主版本不低于 22。
- 在浏览器打开本地地址，并确认能看到 Firefly 首页。

如果一个步骤做完后不知道如何判断成功，它就还不够清晰。

---

## 三、AI 在流程里负责什么

AI 不是替你决定一切的人，更适合扮演四个角色：

| 角色 | AI 可以做什么 |
|---|---|
| 澄清者 | 把模糊需求改写成明确目标 |
| 拆解者 | 把大任务拆成有依赖关系的小步骤 |
| 调试者 | 根据命令、环境和完整报错判断原因 |
| 审查者 | 检查文章、配置、代码和验收结果 |

每次遇到卡点，可以使用这个固定模板：

```text
我的目标是：
我的系统是：
我当前所在目录是：
我已经完成：
我执行的命令是：
完整报错是：
我预期看到：
我实际看到：

请先判断问题属于哪一层，再只给我一个最小执行步骤，并说明验证方法。
```

这里最重要的不是“让 AI 直接给答案”，而是形成闭环：

```text
描述现状 → 得到一个动作 → 执行动作 → 观察结果 → 反馈结果 → 决定下一步
```

---

## 四、阶段 0：先定义完成标准

开始安装软件之前，先确定什么叫“做完”。

- [ ] 能打开 ChatGPT 桌面端并正常提问。
- [ ] Node.js、npm、pnpm 和 Git 都能输出版本号。
- [ ] 自己的 GitHub 账号下存在 Firefly Fork。
- [ ] Firefly 已克隆到本地。
- [ ] `pnpm dev` 能启动开发服务器。
- [ ] 浏览器能打开 Firefly 首页。
- [ ] 至少修改一项站点配置。
- [ ] 至少新增一篇 Markdown 文章。
- [ ] `pnpm check`、`pnpm type-check` 和 `pnpm build` 通过。
- [ ] GitHub Actions 部署成功。
- [ ] `github.io` 线上地址能打开新增文章。

这张列表就是项目的终点。后面的每个阶段都在为它服务。

---

## 五、阶段 1：准备 AI 与开发工具

### ChatGPT 桌面端

- [ ] 打开 [ChatGPT 官方下载页](https://chatgpt.com/download/)。
- [ ] 下载适合当前系统的安装包。
- [ ] 安装并登录 ChatGPT。
- [ ] 新建一个“AI 编程学习”对话。
- [ ] 用前面的提问模板完成一次测试。

### Node.js、pnpm 与 Git

Firefly 当前需要 Node.js 22 或更高版本，项目使用 pnpm 管理依赖。

```powershell
node --version
npm --version
npm install --global pnpm@9.14.4
pnpm --version
git --version
```

每执行一条命令，只判断一件事：**它有没有输出正确的版本号。**

### GitHub Desktop 与 VS Code

- [ ] 安装并登录 GitHub Desktop。
- [ ] 记住 `Clone repository` 和 `Push origin` 的位置。
- [ ] 安装并启动 VS Code。
- [ ] 在 VS Code 中打开一个文件夹。
- [ ] 打开 VS Code 集成终端。

工具的详细安装过程，可以继续阅读：[安装必备环境](/posts/install-required-environment/)。

---

## 六、阶段 2：Fork、克隆并运行 Firefly

### Fork 官方仓库

- [ ] 打开 [CuteLeaf/Firefly](https://github.com/CuteLeaf/Firefly)。
- [ ] 确认仓库所有者是 `CuteLeaf`。
- [ ] 点击 **Fork**。
- [ ] 确认目标账号是自己的 GitHub 账号。
- [ ] 点击 **Create fork**。
- [ ] 记录自己的仓库地址。

### 克隆到本地

- [ ] 打开 GitHub Desktop。
- [ ] 选择 `File → Clone repository`。
- [ ] 选择自己账号下的 Firefly。
- [ ] 选择本地存放目录。
- [ ] 点击 **Clone**。
- [ ] 确认项目根目录中存在 `package.json`。

### 安装依赖并启动

在 Firefly 项目根目录打开终端：

```powershell
pnpm install
pnpm dev
```

终端会显示本地访问地址。端口可能不是固定值，应以终端实际输出为准。

- [ ] 打开终端显示的本地地址。
- [ ] 确认能看到博客首页。
- [ ] 打开一篇已有文章。
- [ ] 回到终端按 `Ctrl+C` 停止服务。
- [ ] 再次执行 `pnpm dev`，确认可以重复启动。

更细的操作可以参考：[Fork Firefly 官方仓库并克隆到本地](/posts/fork-firefly-and-clone/)。

---

## 七、阶段 3：理解结构，再修改配置

不要一进入项目就随便改文件。先建立一个最小地图：

| 路径 | 作用 |
|---|---|
| `package.json` | 记录项目命令和依赖 |
| `src/config/` | 站点名称、作者、页面和功能配置 |
| `src/content/posts/` | Markdown 与 MDX 文章 |
| `src/pages/` | 页面路由 |
| `src/layouts/` | 页面布局 |
| `src/components/` | 可复用界面组件 |
| `public/` | 直接提供给浏览器的静态资源 |
| `.github/workflows/` | GitHub Actions 自动化流程 |

认识结构后，只做一次最小配置修改：

- [ ] 在 `src/config/` 找到站点基础配置。
- [ ] 记录修改前的值。
- [ ] 修改站点名称或描述。
- [ ] 保存文件。
- [ ] 刷新本地首页。
- [ ] 确认修改已经显示。

一次只改一个值，出现问题时就容易知道是哪次修改造成的。

---

## 八、阶段 4：新增第一篇文章

文章位于 `src/content/posts/`。先打开一篇现有文章，观察文件名、frontmatter 和正文结构。

一篇最小文章可以这样开始：

```markdown
---
title: "我的第一篇文章"
published: 2026-07-12T15:00:00+08:00
description: "记录我第一次使用 AI 搭建 Firefly 博客的过程。"
tags: ["AI编程", "Firefly"]
category: "实战篇"
draft: false
---

## 我想解决的问题

我想把 Firefly 在本地运行起来，并写入自己的文章。
```

保存以后完成四项验证：

- [ ] 文章出现在本地文章列表。
- [ ] 文章详情页可以打开。
- [ ] 标题、正文和代码块显示正常。
- [ ] 终端没有新增错误。

---

## 九、阶段 5：把过程重构成文章系列

零散聊天记录很难复用。完成一次流程后，可以把它重构成九篇文章：

1. 清晰的问题才叫问题。
2. 安装 AI 编程工具。
3. Fork 和克隆 Firefly。
4. 把 Firefly 在本地运行起来。
5. 认识 Firefly 文件结构并修改配置。
6. 新增一篇 Markdown 文章。
7. 提交并推送到 GitHub。
8. 使用 GitHub Pages 部署 Firefly。
9. 卡点、搜索和有效信息判断。

每篇文章只解决一个主题，并包含五个固定部分：

```text
问题是什么
适用环境是什么
具体怎么做
如何验证完成
失败时先检查什么
```

这一步就是“重构”：把一次性的经历变成下次可以直接使用的系统。

---

## 十、阶段 6：检查、提交与推送

写完文章后，先在本地完成质量检查：

```powershell
pnpm check
pnpm type-check
pnpm build
pnpm preview
```

四条命令分别回答四个问题：

| 命令 | 验证内容 |
|---|---|
| `pnpm check` | Astro 内容和组件是否有诊断问题 |
| `pnpm type-check` | TypeScript 类型是否正确 |
| `pnpm build` | 网站能否生成正式构建产物 |
| `pnpm preview` | 构建结果在浏览器中是否正常 |

检查通过后再查看 Git 变更：

```powershell
git status
git diff
```

确认没有密码、Token、临时文件和无关修改，再提交：

```powershell
git add .
git commit -m "feat: add AI programming learning article"
git push origin master
```

---

## 十一、阶段 7：部署到 GitHub Pages

当前 Firefly 仓库已经包含 `.github/workflows/deploy.yml`，它会使用 GitHub Actions 构建并部署网站。

### 部署前检查

- [ ] 工作流监听的分支和实际推送分支一致。
- [ ] Node.js 版本符合项目要求。
- [ ] pnpm 版本与 `package.json` 一致。
- [ ] 构建命令是 `pnpm run build`。
- [ ] 工作流包含 `actions/deploy-pages`。

### GitHub 设置

1. 打开自己的 GitHub 仓库。
2. 进入 **Settings → Pages**。
3. 将部署来源设置为 **GitHub Actions**。
4. 推送代码后打开 **Actions** 页面。
5. 找到 **Deploy to GitHub Pages** 工作流。
6. 依次检查 `build` 和 `deploy` 是否成功。

仓库名称决定最终地址：

| 仓库名 | 网站地址 |
|---|---|
| `用户名.github.io` | `https://用户名.github.io/` |
| 其他仓库名 | `https://用户名.github.io/仓库名/` |

部署成功后，不要只看首页，还要验证：

- [ ] 首页能打开。
- [ ] 新文章能打开。
- [ ] 刷新文章详情页仍然正常。
- [ ] 图片、CSS 和 JavaScript 能加载。
- [ ] 无痕窗口中也能访问。
- [ ] 手机宽度下页面没有明显错位。

---

## 十二、卡点出现时，怎样搜索信息

遇到错误时，先缩小问题，再搜索。

不要搜索：

> Firefly 为什么不能用？

应该搜索：

> Firefly 6.13.6 在 Node.js 22 下执行 pnpm build 时出现某条具体错误。

判断资料是否有效，可以检查五件事：

1. 来源是否是官方文档、官方仓库或可靠维护者。
2. 内容的发布时间是否过旧。
3. 资料适用的版本是否和本地版本一致。
4. 方法是否能用最小例子验证。
5. 实际结果是否符合资料描述。

如果网上教程和本地项目冲突，优先顺序应该是：

```text
本地项目实际配置 → 官方仓库 → 官方文档 → 可靠社区文章 → 普通搜索结果
```

---

## 十三、卡点记录模板

每次解决一个真实问题，都复制下面的模板：

```markdown
## 卡点名称

- 当前目标：
- 当前目录：
- 系统和版本：
- 执行的命令：
- 完整报错：
- 预期结果：
- 实际结果：
- 猜测原因：
- 查到的资料：
- AI 给出的最小下一步：
- 最终解决方法：
- 验证方法：
- 下次如何更快判断：
```

记录不是额外负担。它会逐渐变成只属于自己的故障手册和问题解决系统。

---

## 十四、最后复盘：你真正学到的是什么

完成这个项目后，收获不只是一个博客，也不是记住几条命令。

真正学到的是一条可迁移的路径：

```text
明确结果
→ 识别输入、输出和约束
→ 分解阶段与依赖
→ 找到最小执行单位
→ 执行并验证
→ 卡点时搜索和调试
→ 把成功过程重构成模板
→ 在下一个问题中复用
```

以后面对“做饭、写作、设计、学会计、做网站”这类大问题，也可以使用同一套结构。

AI 的价值不只是替你写代码，而是帮助你更快地澄清、拆解、执行和复盘。真正需要自己建立的能力，是：

> **知道自己要什么，知道下一步做什么，也知道怎样判断它做对了。**

---

## 参考资料

- [ChatGPT 官方下载页](https://chatgpt.com/download/)
- [Node.js 官方下载页](https://nodejs.org/en/download)
- [Git 官方下载页](https://git-scm.com/downloads)
- [GitHub Desktop 官方下载页](https://desktop.github.com/download/)
- [VS Code 官方下载页](https://code.visualstudio.com/download)
- [Firefly 官方仓库](https://github.com/CuteLeaf/Firefly)
- [Firefly 官方文档](https://docs-firefly.cuteleaf.cn/)
- [GitHub Pages 文档](https://docs.github.com/pages/)
- [适用于小白的 Firefly 博客部署教程](https://blog.olinl.com/posts/firefly-first/)

---

*从零开始学 AI 编程 · Firefly 实战路线图*
