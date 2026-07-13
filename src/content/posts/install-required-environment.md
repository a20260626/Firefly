---
title: "安装 AI 编程工具"
published: 2026-07-12T16:30:00+08:00
updated: 2026-07-13T13:00:00+08:00
pinned: false
description: "为 AI 编程和 Firefly 本地开发准备 ChatGPT、Node.js、pnpm、Git、GitHub Desktop 与 VS Code，并逐项验证工具是否可用。"
tags: [AI编程, Firefly, 环境配置, Node.js, pnpm, Git]
category: 博客指南
draft: false
image: ./images/nodejs-download.png
---

安装工具不是学习 AI 编程的终点，而是让后续每个动作都能够执行和验证的基础。本篇只解决一个主题：在 Windows 电脑上准备 Firefly 所需的 AI 编程工具。

工具安装完成后，你应该能够打开 ChatGPT，使用终端检查 Node.js、npm、pnpm 和 Git 的版本，使用 GitHub Desktop 管理仓库，并用 VS Code 修改项目文件。

## 安装前先理解三个概念

初学者最容易把“下载完成”“安装完成”和“可以使用”混为一谈。它们实际上是三个不同状态：

```text
下载安装包 → 运行安装程序 → 在新终端或应用中验证
```

- **下载安装包**只是把一个文件保存到电脑。
- **运行安装程序**会把软件复制到系统目录，并可能更新环境变量。
- **验证**才证明 Windows 和当前终端能够找到这个工具。

### 终端是什么

PowerShell、Windows Terminal 和 VS Code 集成终端都是输入命令并查看结果的窗口。本文示例使用 PowerShell。同一条命令在这些终端中通常有相同作用，但它们打开时所在的目录可能不同。

终端提示符中通常会显示当前目录，例如：

```powershell
PS C:\Users\你的用户名>
```

安装工具时，当前目录通常不重要；以后执行 Firefly 项目命令时，则必须先进入包含 `package.json` 的项目根目录。

### PATH 环境变量是什么

当你输入 `node`、`pnpm` 或 `git` 时，Windows 会在 PATH 记录的目录中寻找对应程序。安装程序可能已经正确安装软件，但旧终端仍保留安装前的 PATH，因此会提示“无法识别命令”。

所以每安装完一个命令行工具，都应关闭旧终端，重新打开 PowerShell，再检查版本。重新打开终端不是仪式，而是让它读取最新系统环境。

### 版本号怎么看

常见版本号由点号分隔，例如 `22.17.0`：

- 第一段 `22` 是主版本。
- 第二段 `17` 是次版本。
- 第三段 `0` 是修订版本。

“Node.js 主版本不低于 22”的意思是第一段数字至少为 22，不是把整个版本号当作小数比较。

## 问题是什么

“我要开始 AI 编程”仍然太模糊。安装工具这一步需要把目标写成可以检查的问题：

> 我使用 Windows 电脑，准备学习 AI 编程并运行 Firefly。请安装 ChatGPT 桌面端、Node.js LTS、pnpm、Git、GitHub Desktop 和 VS Code，并逐项告诉我如何验证安装成功。

这句话包含了本篇任务的五个边界：

| 要素 | 本篇内容 |
| --- | --- |
| 目标 | 准备可以进行 AI 编程和 Firefly 开发的电脑环境 |
| 输入 | Windows 电脑、网络、GitHub 账号和安装权限 |
| 工具 | ChatGPT、Node.js、pnpm、Git、GitHub Desktop、VS Code |
| 约束 | Node.js 版本不低于 22，项目使用 pnpm |
| 验收 | 每个工具都能打开或在终端输出可识别的版本信息 |

本篇不处理 Fork 仓库、安装项目依赖或启动 Firefly。工具都准备好以后，再进入下一篇：[Fork 和克隆 Firefly](/posts/fork-firefly-and-clone/)。

## 适用环境是什么

本文适用于下面的环境：

- Windows 10 或 Windows 11。
- 可以访问 ChatGPT、Node.js、GitHub 和 VS Code 官方网站。
- 拥有安装软件的权限。
- 准备使用 PowerShell、Windows Terminal 或 VS Code 集成终端。
- 使用 Node.js 22 或更高版本的 LTS 版本。

工具的职责不同，不要把它们混为一个软件：

| 工具 | 用途 |
| --- | --- |
| ChatGPT 桌面端 | 说明问题、拆分任务、分析报错和辅助编程 |
| Node.js | 运行 Astro、脚本和前端开发工具 |
| npm | 随 Node.js 安装的包管理器，用来安装 pnpm |
| pnpm | Firefly 项目指定的依赖安装和脚本执行工具 |
| Git | 记录文件变化、提交代码和与远程仓库同步 |
| GitHub Desktop | 用图形界面管理 GitHub 仓库 |
| VS Code | 查看项目文件、修改配置和编写 Markdown |

## 推荐安装顺序

按照依赖关系安装，可以让每一步都独立验证：

1. ChatGPT：先准备提问和记录问题的工具。
2. Node.js：提供项目运行环境，同时带来 npm。
3. pnpm：依赖 Node.js 和 npm，因此必须在 Node.js 验证成功后安装。
4. Git：提供版本控制命令。
5. GitHub Desktop：使用 Git 和 GitHub 账号管理远程仓库。
6. VS Code：最后准备项目编辑环境。

开始前还应确认：

- [ ] Windows 账户拥有安装软件的权限。
- [ ] 磁盘有足够空间保存软件、项目和依赖。
- [ ] 浏览器可以正常访问各工具的官方网站。
- [ ] 知道文件下载到了哪个目录。
- [ ] 已关闭不再使用的旧安装程序，避免重复安装。
- [ ] 不从网盘、论坛附件或不明镜像下载可执行文件。

## 具体怎么做

### 1. 安装 ChatGPT 桌面端

打开 [ChatGPT 官方下载页面](https://chatgpt.com/download/)，下载适合当前 Windows 系统的安装程序。

![ChatGPT Windows 桌面端下载入口](./images/chatgpt-download-windows.png)

安装完成后打开 ChatGPT，并登录自己的账号。新建一个“AI 编程学习”对话，后续可以把项目路径、命令输出和完整报错集中记录在这里。

第一次提问可以使用这段文字：

```text
我准备在 Windows 上学习 AI 编程，并运行 Firefly 博客。
请先帮我检查需要准备哪些工具，但一次只给我一个最小执行步骤，
并告诉我每一步如何验证完成。
```

登录后先做一次最小测试，例如询问“请复述我的目标，并只给出一个检查 Node.js 版本的步骤”。确认能够正常发送消息和收到回答即可。此时不需要安装插件，也不需要把 GitHub 密码、验证码、访问令牌或私人文件发给 AI。

建议为 Firefly 单独保留一个对话，并在每次反馈中写出：执行的命令、完整结果、预期结果和当前目录。这样后续排错不必反复重述背景。

### 2. 安装 Node.js

打开 [Node.js 官方下载页面](https://nodejs.org/zh-cn/download)，选择 LTS 稳定版。Firefly 当前要求 Node.js 主版本不低于 22。

![下载 Node.js](./images/nodejs-download.png)

运行安装程序，保持默认选项完成安装。安装结束后，关闭原来的终端窗口并重新打开 PowerShell，执行：

```powershell title="检查 Node.js 和 npm"
node --version
npm --version
```

![验证 Node.js 和 npm 版本号](./images/nodejs-version-check.png)

确认第一条命令输出的 Node.js 主版本是 `22` 或更高。

选择版本时优先使用 LTS，而不是为了数字更大选择实验性版本。LTS 更适合学习和长期维护的项目。安装过程中如果看到“添加到 PATH”之类的选项，应保持启用；没有明确需要时，不必修改默认安装目录。

验证时需要同时检查 Node.js 和 npm，是因为 pnpm 的安装依赖 npm。如果 `node --version` 成功而 `npm --version` 失败，环境仍然没有准备完整，不应继续安装 pnpm。

### 3. 安装 pnpm

Node.js 安装完成后，系统会同时提供 npm。使用 npm 全局安装项目使用的 pnpm：

```powershell title="安装 pnpm"
npm install --global pnpm@9.14.4
```

然后检查 pnpm 版本：

```powershell title="检查 pnpm"
pnpm --version
```

![安装并验证 pnpm](./images/pnpm-install-check.png)

如果能输出版本号，说明终端已经可以识别 pnpm。Firefly 后续的依赖安装、开发和构建命令都应使用 pnpm。

这里指定 `9.14.4` 是为了与当前 Firefly 项目声明的包管理器版本保持一致。不要因为电脑上已经有 npm，就把后续 `pnpm install` 随意替换成 `npm install`；不同包管理器会生成不同锁文件，也可能改变依赖解析结果。

可以再执行：

```powershell title="查看 pnpm 的程序位置"
Get-Command pnpm
```

如果版本命令成功，这一步不是必须的；它主要用于排查电脑中是否同时存在多个 pnpm 安装位置。

### 4. 安装 Git

打开 [Git 官方下载页面](https://git-scm.com/downloads)，下载 Git for Windows 安装包。安装时保持默认选项即可。

![下载 Git for Windows](./images/git-download.png)

安装完成后重新打开终端，执行：

```powershell title="检查 Git"
git --version
```

Git 用于克隆 Firefly、查看文件变化、创建提交以及推送到 GitHub。

Git 安装程序包含较多选项。初次安装可以保留默认值，不必为了“优化”而修改换行符、默认终端、凭据管理器等选项。安装完成后除了版本号，还可以检查 Git 是否能够读取全局配置：

```powershell title="查看 Git 配置"
git config --global --list
```

第一次运行时没有输出用户名和邮箱并不代表 Git 安装失败，只说明尚未配置提交身份。提交身份可以在真正创建提交前再设置，不要为了填满配置而随意写入错误信息。

### 5. 安装并登录 GitHub Desktop

打开 [GitHub Desktop 官方下载页面](https://desktop.github.com/)，下载安装程序。安装完成后启动 GitHub Desktop，登录自己的 GitHub 账号。

![GitHub Desktop 下载入口](./images/github-desktop-download.png)

![登录 GitHub Desktop](./images/github-desktop-sign-in.png)

登录后，确认界面能够显示当前账号。后续会使用 **Clone repository** 克隆 Firefly，使用 **Commit to master** 创建提交，并使用 **Push origin** 推送代码。

Git 和 GitHub Desktop 不是二选一：Git 是底层版本控制工具，GitHub Desktop 是更容易观察改动、提交和推送的图形界面。即使主要使用 GitHub Desktop，也保留 Git 命令行工具，方便查看版本和排查问题。

登录通常会打开浏览器完成授权。请确认浏览器地址属于 GitHub 官方域名，不要把一次性验证码或访问令牌复制给他人。登录完成后返回 GitHub Desktop，核对显示的是准备存放 Firefly Fork 的账号。

### 6. 安装并打开 VS Code

打开 [VS Code 官方下载页面](https://code.visualstudio.com/)，下载适合 Windows 的版本并完成安装。

![下载 VS Code](./images/vscode-download.png)

安装完成后打开 VS Code。现在还没有 Firefly 项目目录，可以先确认 VS Code 能正常启动；克隆仓库后，再使用 **File → Open Folder** 打开 Firefly 根目录。

安装时如果提供“添加到 PATH”“在资源管理器中添加打开方式”等选项，可以按需要启用，但它们不是 Firefly 运行的必要条件。第一次打开 VS Code 时也不必立即安装大量扩展。先使用内置的文件浏览器、编辑器和终端完成最小流程，遇到明确需求时再添加扩展。

可以通过菜单 **Terminal → New Terminal** 打开集成终端，并执行前面四条版本命令。这样能确认 VS Code 启动的终端也能读取 Node.js、pnpm 和 Git。如果外部 PowerShell 可以识别命令，而 VS Code 终端不能，完全退出并重新打开 VS Code，再重新测试。

### 7. 建立工具检查记录

把版本和账号状态记录在自己的学习笔记中：

```text
操作系统：Windows
Node.js：
npm：
pnpm：
Git：
GitHub Desktop：已登录 / 未登录
VS Code：已打开 / 未打开
ChatGPT：已登录 / 未登录
```

不要只写“已经安装”。记录实际输出，后面遇到版本不匹配时才能快速定位问题。

一份填写完成的记录可以是：

```text
检查日期：2026-07-13
操作系统：Windows 11
使用终端：PowerShell
Node.js：v22.17.0
npm：10.x.x
pnpm：9.14.4
Git：2.x.x.windows.x
GitHub Desktop：已登录，账号已核对
VS Code：可以启动，集成终端可识别上述命令
ChatGPT：已登录，最小测试通过
```

不要照抄示例中的版本号，应记录自己电脑的真实输出。以后项目出现“在别人电脑上可以运行”的情况，这张记录可以快速比较环境差异。

## 如何验证完成

依次完成下面的检查：

- [ ] ChatGPT 桌面端可以打开。
- [ ] ChatGPT 账号已经登录。
- [ ] Node.js 主版本不低于 `22`。
- [ ] `npm --version` 能输出版本号。
- [ ] `pnpm --version` 能输出版本号。
- [ ] `git --version` 能输出版本号。
- [ ] GitHub Desktop 可以打开。
- [ ] GitHub Desktop 已登录自己的账号。
- [ ] VS Code 可以打开。
- [ ] 已记录所有工具的版本或登录状态。

也可以在 PowerShell 中一次执行下面的检查：

```powershell title="批量检查开发工具"
node --version
npm --version
pnpm --version
git --version
```

完成标准不是“安装程序运行结束”，而是：命令能够被终端识别、版本符合要求、图形工具能够打开并登录。

### 做一次交叉验证

除了在 PowerShell 中批量检查，还可以在 VS Code 集成终端里重复执行一次。两处输出应该一致。如果版本不同，说明电脑中可能存在多个安装位置，或者两个程序读取的 PATH 不同。

可以使用下面的命令查看实际调用位置：

```powershell title="查看命令来源"
Get-Command node
Get-Command npm
Get-Command pnpm
Get-Command git
```

正常情况下，每条命令都会显示一个可执行文件或脚本的来源路径。只有在版本冲突或命令来源可疑时才需要进一步处理，不要仅因为路径看起来陌生就删除文件。

### 保存验收证据

建议把版本输出复制到学习笔记，而不是只保留截图。文本更容易搜索、比较和发给 AI 分析。截图可以用于记录 GitHub Desktop 登录状态或 VS Code 界面，但要避免包含邮箱、私人仓库名称和其他不必要的个人信息。

## 失败时先检查什么

### 终端提示“不是内部或外部命令”

先关闭当前终端，再打开一个新的 PowerShell 窗口，然后重新执行命令。安装程序通常会更新 PATH 环境变量，旧终端不会自动读取新配置。

如果仍然失败，按顺序检查：

1. 软件安装程序是否真的运行完成。
2. 是否打开了安装软件之后的新终端。
3. Windows 的应用执行别名是否产生了冲突。
4. PATH 中是否包含对应软件的安装目录。

还可以用下面的形式单独确认命令是否存在：

```powershell
Get-Command node
```

如果提示找不到命令，说明问题仍在安装或 PATH，而不是 Firefly 项目本身。如果能返回路径，但 `node --version` 失败，应记录两条命令的完整输出，让 AI 根据具体错误继续判断。

### Node.js 版本低于 22

卸载或更新旧版本后，重新安装 Node.js LTS。不要通过修改项目配置绕过版本要求，因为后续构建可能仍然失败。

更新后若仍显示旧版本，使用 `Get-Command node` 查看当前终端实际调用的路径。电脑上可能保留了旧安装位置。不要直接搜索并批量删除所有名为 `node.exe` 的文件，应先确认每个路径属于哪个安装方式。

### pnpm 安装失败

先确认 `node --version` 和 `npm --version` 都能正常输出，再重新执行：

```powershell
npm install --global pnpm@9.14.4
```

如果 npm 报权限错误，可以使用管理员 PowerShell 重试；如果网络错误，先记录完整报错，再搜索具体错误信息。

不要把所有 npm 错误都归类为“网络问题”。可以根据关键现象先分类：

| 现象 | 优先检查 |
| --- | --- |
| `npm` 本身无法识别 | Node.js/npm 安装与 PATH |
| `EACCES`、`EPERM` | 权限、文件占用或安全软件拦截 |
| `ETIMEDOUT`、连接重置 | 网络连接、代理或源可达性 |
| 安装成功但 `pnpm` 无法识别 | 新终端、全局安装目录与 PATH |
| pnpm 版本与项目要求不同 | 安装命令中的明确版本号 |

一次只处理当前错误。不要同时更换 npm 镜像、关闭安全软件、修改 PATH 和重新安装 Node.js，否则即使恢复正常，也无法知道真正原因。

### GitHub Desktop 无法登录

确认浏览器可以登录 GitHub，并检查 GitHub Desktop 是否使用了正确账号。不要把密码、验证码或访问令牌复制到聊天记录中。

如果浏览器显示授权成功，但桌面端仍停留在登录页面，先完全关闭 GitHub Desktop 后重新打开，并确认系统默认浏览器没有阻止回到应用。不要连续创建多个 GitHub 账号来绕过登录问题。

### VS Code 终端识别不到新工具

如果 PowerShell 独立窗口能执行版本命令，而 VS Code 集成终端不能，通常是因为 VS Code 在安装工具之前已经启动，仍保留旧环境。

按顺序处理：

1. 关闭 VS Code 中现有的终端。
2. 完全退出所有 VS Code 窗口。
3. 重新启动 VS Code。
4. 新建集成终端并再次检查版本。

只有外部 PowerShell 和重启后的 VS Code 都无法识别命令时，才继续检查系统安装和 PATH。

### 安装程序要求管理员权限

先确认安装包来自官方网站，再阅读 Windows 提示中显示的发布者和程序名称。管理员权限意味着安装程序可以修改系统目录，不应对来源不明的文件授权。

如果使用的是单位或学校管理的电脑，权限可能受到策略限制。此时不要尝试绕过管理策略，应联系设备管理员，说明需要安装的软件名称、用途和官方来源。

### 下载速度慢或页面打不开

先区分是单个官方网站不可访问，还是整个网络异常。保留目标网址和浏览器错误信息，再决定是否重试。不要为了加快下载随意使用不明第三方安装包；开发工具被篡改会影响后续所有项目。

### 不知道某个工具是否真的需要

回到工具职责表逐项判断：Node.js 和 pnpm 负责运行项目，Git 和 GitHub Desktop 负责管理代码，VS Code 负责编辑文件，ChatGPT 负责协助思考和排查。当前步骤只需完成工具准备，不需要提前学习所有功能。

## 向 AI 反馈安装问题的模板

当某一步失败时，可以复制下面的模板：

```text
我的目标是：安装并验证（工具名称）。
操作系统是：Windows 10 / Windows 11。
使用的终端是：PowerShell / VS Code 集成终端。
安装包来源是：（官方网站地址）。
我执行的命令是：
完整输出是：
我预期看到的是：
我已经尝试过：

请先判断问题属于下载、安装、PATH、权限、网络还是版本冲突，
然后只给我一个最小检查步骤，并说明怎样判断结果。
```

如果涉及命令行错误，优先复制文字；如果涉及安装界面，再补充截图。发送前检查截图和路径中是否包含真实姓名、邮箱、Token 或私人目录信息。

## 安装结束后的整理

环境验证完成后，可以做一次简单整理：

- 删除确认不再需要的重复安装包，但不要删除已安装程序目录。
- 保存官方下载安装页面，方便以后更新或重新安装。
- 保留工具版本记录和检查日期。
- 记下自己使用的终端类型。
- 不要立即升级所有工具；先用当前已验证环境完成 Firefly 本地运行。

环境搭建的目标是得到一个稳定起点，而不是永远追逐最新版本。后续如果项目可以正常安装依赖、启动和构建，就应优先保持环境稳定；只有明确的兼容性、安全性或功能需求出现时再升级。

如果每个工具都通过了独立验证，阶段 1 就完成了。下一步进入：[Fork 和克隆 Firefly](/posts/fork-firefly-and-clone/)。
