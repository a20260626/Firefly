---
title: "把 Firefly 在本地运行起来"
published: 2026-07-12T17:30:00+08:00
updated: 2026-07-12T17:30:00+08:00
pinned: false
description: "在 Windows 上为 Firefly 安装项目依赖，启动 Astro 开发服务器，打开本地网站，并用热更新确认项目已经可以继续修改。"
tags: [AI编程, Firefly, 本地开发, Astro, pnpm]
category: 博客指南
draft: false
---

完成 [Fork 和克隆 Firefly](/posts/fork-firefly-and-clone/) 后，代码已经在自己的电脑上了，但“代码存在”不等于“项目可以运行”。本篇把目标缩小为一条可以重复执行的闭环：进入正确目录、安装依赖、启动开发服务器、打开本地页面，并确认修改后页面能够更新。

## 问题是什么

“把 Firefly 跑起来”还不够具体。可以把它改写成下面这个可执行问题：

> 我已经把自己的 Firefly 仓库克隆到 Windows 本地。现在请在项目根目录安装依赖，启动开发服务器，打开终端输出的本地地址，并验证首页能够显示、Markdown 修改能够触发更新。

这一步的输入、过程和输出如下：

| 要素 | 本篇内容 |
| --- | --- |
| 输入 | 已经克隆到本地的 Firefly 项目目录 |
| 环境 | Node.js 22 或更高版本、pnpm 9 或更高版本 |
| 执行 | `pnpm install` 和 `pnpm dev` |
| 输出 | 一个可以在浏览器访问的本地 Firefly 网站 |
| 验收标准 | 首页能打开，终端没有启动错误，修改内容后页面能更新 |

本地运行只服务于当前电脑。它和后面的 GitHub Pages 部署是两件事：本地运行用于编辑和预览，部署用于让其他人通过互联网访问。

## 适用环境是什么

本文适用于下面的环境：

- Windows 10 或 Windows 11。
- 已经完成 GitHub Fork，并把自己的 Firefly 仓库克隆到本地。
- 已经安装 Node.js LTS，版本不低于 22。
- 已经安装 pnpm，版本不低于 9。
- 已经安装 VS Code 或其他可以编辑 Markdown 的编辑器。
- 当前项目使用 Astro，开发命令由项目根目录的 `package.json` 提供。

开始前，建议先完成上一篇：[Fork 和克隆 Firefly](/posts/fork-firefly-and-clone/)。如果项目还没有出现在电脑上，不要直接执行 `pnpm install`，先把仓库克隆完成。

## 具体怎么做

### 1. 打开 Firefly 项目根目录

在文件管理器中打开克隆后的 `Firefly` 文件夹，在文件夹空白处右键打开 PowerShell。也可以直接在 PowerShell 中进入目录：

```powershell
cd E:\Documents\GitHub\Firefly
```

路径要替换成自己的实际目录。进入后先确认当前目录确实是项目根目录：

```powershell
Get-Location
Get-ChildItem package.json, pnpm-lock.yaml
```

如果能够看到 `package.json` 和 `pnpm-lock.yaml`，说明终端位置正确。后面的命令都应该在这里执行。

### 2. 检查基础工具版本

先不要急着安装依赖，确认 Node.js、pnpm 和 Git 可以被终端识别：

```powershell
node --version
pnpm --version
git --version
```

版本至少应该满足：

```text
Node.js >= 22
pnpm >= 9
```

如果命令输出版本号，说明工具已经进入当前终端的 PATH。版本过低时，先完成 [安装 AI 编程工具](/posts/install-required-environment/)，再继续本篇。

### 3. 安装项目依赖

在项目根目录执行：

```powershell
pnpm install
```

这个命令会根据 `package.json` 和 `pnpm-lock.yaml` 下载项目依赖，并生成或更新本地依赖目录。首次安装可能需要一些时间，期间不要关闭终端。

安装结束后，可以检查项目目录中是否出现 `node_modules`：

```powershell
Test-Path node_modules
```

输出 `True` 只代表依赖目录存在，最终是否安装成功仍然要以 `pnpm dev` 能否启动为准。

### 4. 启动 Astro 开发服务器

继续在同一个项目根目录执行：

```powershell
pnpm dev
```

终端会显示类似下面的地址：

```text
Local   http://localhost:4321/
```

以终端实际显示的地址为准。如果 `4321` 端口已经被其他程序占用，Astro 可能会自动使用另一个端口，例如 `4322`。不要猜端口，直接复制终端输出的 `Local` 地址。

### 5. 在浏览器中打开本地网站

把终端显示的地址粘贴到浏览器中。看到 Firefly 首页后，先检查：

- 页面不是空白页。
- 浏览器地址栏使用的是本地地址，而不是 GitHub Pages 地址。
- 终端没有持续出现红色错误。
- 首页标题、文章列表和导航能够正常显示。

开发服务器运行期间，终端窗口需要保持打开。按 `Ctrl+C` 会停止本地服务。

### 6. 用一处小修改验证热更新

不要一开始就修改大量文件。可以用 VS Code 打开一篇自己的 Markdown 文章，在正文末尾临时增加一句文字并保存，然后回到浏览器：

```markdown
> 本地开发服务器已经可以正常更新页面。
```

如果浏览器自动刷新后能看到这句话，说明“编辑文件 → 开发服务器重新构建 → 浏览器显示结果”的最小闭环已经完成。验证结束后，可以删除这句临时文字，避免把测试内容带入正式文章。

### 7. 停止和重新启动服务

完成验证后，在运行 `pnpm dev` 的终端按：

```text
Ctrl+C
```

需要继续工作时，再回到项目根目录执行：

```powershell
pnpm dev
```

能够重复启动，说明本地运行步骤已经可以被下次直接复用。

## 如何验证完成

依次完成下面的检查：

- [ ] 当前终端目录包含 `package.json` 和 `pnpm-lock.yaml`。
- [ ] `node --version` 显示 Node.js 22 或更高版本。
- [ ] `pnpm --version` 显示 pnpm 9 或更高版本。
- [ ] `git --version` 能正常输出版本号。
- [ ] `pnpm install` 执行完成且没有未处理的错误。
- [ ] 项目目录中存在 `node_modules`。
- [ ] `pnpm dev` 能启动 Astro 开发服务器。
- [ ] 浏览器能打开终端输出的 `Local` 地址。
- [ ] Firefly 首页能够显示，不是空白页。
- [ ] 浏览器地址是本地地址，而不是线上地址。
- [ ] 修改并保存 Markdown 后，浏览器能够看到更新结果。
- [ ] 按 `Ctrl+C` 后服务能够停止，再次执行 `pnpm dev` 能重新启动。

当这些项目都完成时，就可以认为 Firefly 已经在本地运行起来了。下一步可以继续：[认识 Firefly 文件结构并修改配置](/posts/setup-project-foundation/)。

## 失败时先检查什么

### `pnpm` 不是内部或外部命令

先执行：

```powershell
node --version
npm --version
```

如果 Node.js 也无法识别，说明 Node.js 没有正确安装或终端还没有刷新 PATH。重新打开 PowerShell，再按照 [安装 AI 编程工具](/posts/install-required-environment/) 检查安装步骤。

### `pnpm install` 失败

先确认三件事：

1. 当前目录确实包含 `package.json`。
2. Node.js 版本满足项目要求。
3. 终端能够访问 npm 镜像或其他依赖下载地址。

不要只复制“安装失败”四个字给 AI。应当把完整错误、执行的命令、Node.js 版本和当前目录一起提供，让 AI 判断是目录、版本、权限还是网络问题。

### `pnpm dev` 找不到命令或启动失败

先重新执行：

```powershell
Get-Location
Get-ChildItem package.json
pnpm install
pnpm dev
```

如果错误提示端口被占用，使用终端新输出的地址；如果错误来自某个配置文件或 Markdown 文件，保留完整报错，再只检查报错指向的文件。

### 浏览器打开后是空白页

先打开浏览器开发者工具的 Console，查看是否有红色错误。然后确认终端是否出现构建错误，以及浏览器打开的是否是当前 `pnpm dev` 输出的地址。把错误信息、最近修改的文件和复现步骤一起交给 AI，不要只描述“页面白了”。

### 修改文件后浏览器没有更新

先确认文件确实保存在当前项目目录，浏览器打开的地址确实来自当前开发服务器。可以手动刷新一次；如果仍然没有变化，再查看终端是否出现编译错误。修改能够生效后，再进入下一篇的配置和内容工作。
