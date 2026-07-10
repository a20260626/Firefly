---
title: "新增文章后构建失败：从路径、slug 到 frontmatter 一步步排查"
published: 2026-07-03T16:06:21+08:00
updated: 2026-07-03T16:06:21+08:00
pinned: false
description: "上一篇我们讲了第一次 Debug 的基本方法："
tags: ["AI编程", "ai编程助手", "ai IDE/编辑器", "前端基础", "版本控制"]
category: "调试篇"
draft: false
---

> 调试最好的练习，不是背很多错误，而是拿一个真实场景拆开看。
> 这一篇用"新增文章后构建失败"做例子，练习如何判断问题出在文件路径、文章 slug，还是 Markdown 的 frontmatter。

---

上一篇我们讲了第一次 Debug 的基本方法：

- 先描述现象
- 再复制完整错误
- 说明最近改了什么
- 一次只改一个可疑点
- 修完以后运行构建验证

这一篇我们拿一个具体场景来练习。

场景很常见：

> **你新增了一篇 Markdown 文章，注册到文章列表里，然后 `npm run build` 失败了。**

这类问题非常适合新手练习调试。

因为它通常不会太复杂，但包含了几个很重要的排查思路：

1. 文件路径是否正确
2. import 名称是否正确
3. slug 是否和 URL 一致
4. frontmatter 是否完整
5. stage / tools 是否和 taxonomy 匹配

掌握这类问题后，你对内容站的数据流会清楚很多。

---

## 先还原现场

假设你刚刚新增了一篇文章：

```text
src/content/articles/my-new-debug-note.md
```

然后你在 `src/data/articles.ts` 里导入：

```ts
import myNewDebugNoteContent from '../content/articles/my-new-debug-note.md?raw';
```

并注册：

```ts
{
  slug: 'my-new-debug-note',
  raw: myNewDebugNoteContent,
}
```

看起来都对。

但运行：

```bash
npm run build
```

结果失败了。

这时不要急着大改。

先把错误信息完整复制出来。

---

## 第一类错误：找不到 Markdown 文件

最常见的错误之一是：

```text
Cannot find module '../content/articles/my-new-debug-note.md?raw'
```

或者 TypeScript 会提示：

```text
error TS2307: Cannot find module '../content/articles/my-new-debug-note.md?raw'
```

这个错误基本在说：

> 你在 `articles.ts` 里导入了一个文件，但项目找不到它。

先检查三个地方。

| 检查点 | 常见问题 |
|--------|----------|
| 文件是否存在 | Markdown 文件根本没创建 |
| 文件名是否一致 | `my-new-debug-note.md` 写成了 `my-new-debug-notes.md` |
| 路径是否正确 | 导入路径层级写错 |

这类问题优先看文件名。

尤其注意：

- 单复数
- 连字符
- 大小写
- `.md` 后缀
- 文件是否真的在 `src/content/articles` 里

你可以让 AI 这样帮你：

```text
这是构建错误：
[粘贴 Cannot find module 错误]

我刚才新增了文章文件，并修改了 src/data/articles.ts。
请只检查导入路径和文件名是否一致，不要改其他逻辑。
```

这能让 AI 聚焦在最可能的原因上。

---

## 第二类错误：import 名称写错

还有一种错误是文件路径没问题，但变量名不一致。

比如你导入时写：

```ts
import myNewDebugNoteContent from '../content/articles/my-new-debug-note.md?raw';
```

但注册时写成：

```ts
{
  slug: 'my-new-debug-note',
  raw: myNewDebugContent,
}
```

这时构建可能会提示：

```text
Cannot find name 'myNewDebugContent'
```

这个错误的意思是：

> 你使用了一个变量名，但这个变量没有定义。

排查方法很简单：

1. 看 import 左边的变量名
2. 看 articleSources 里 `raw` 使用的变量名
3. 确认两者完全一致

这类问题不需要改 Markdown。

它只需要改 `src/data/articles.ts`。

调试时要避免被带偏。

如果错误是变量名找不到，就先不要去改 CSS、组件或文章正文。

---

## 第三类错误：frontmatter 缺字段

本站每篇文章都必须有 frontmatter。

也就是 Markdown 文件最上面的这段：

```yaml
---
title: 新增文章后构建失败
stage: 调试篇
tools:
  - ai编程助手
  - ai IDE/编辑器
updatedAt: 2026-07-03
---
```

如果少了字段，项目解析文章时就会报错。

比如少了 `tools`：

```yaml
---
title: 新增文章后构建失败
stage: 调试篇
updatedAt: 2026-07-03
---
```

可能会出现类似错误：

```text
Article "my-new-debug-note" has invalid frontmatter.
```

这个错误的关键是：

> 文章文件找到了，但元数据不符合项目要求。

这时要检查：

| 字段 | 是否必需 | 示例 |
|------|----------|------|
| `title` | 是 | 新增文章后构建失败 |
| `stage` | 是 | 调试篇 |
| `tools` | 是 | 列表格式 |
| `updatedAt` | 是 | 2026-07-03 |

特别注意 `tools` 必须是列表。

正确：

```yaml
tools:
  - ai编程助手
  - 前端基础
```

不推荐：

```yaml
tools: ai编程助手
```

因为当前解析逻辑期待的是数组。

---

## 第四类错误：stage 名称不匹配

还有一种常见错误是 stage 写错。

比如写成：

```yaml
stage: 调试
```

但 taxonomy 里真正的阶段名称是：

```text
调试篇
```

这时可能会报：

```text
Article "my-new-debug-note" uses unknown stage "调试".
```

这个错误很清楚：

> 文章声明了一个不存在的阶段。

解决方式不是随便改代码。

而是对照 `src/data/taxonomy.ts`。

当前合法阶段是：

1. 认知篇
2. 起步篇
3. 对话篇
4. 实战篇
5. 调试篇
6. 发布篇

frontmatter 里的 `stage` 必须和这些名字完全一致。

包括中文字符也要一致。

少一个"篇"，就会被认为是未知阶段。

---

## 第五类错误：tools 名称不匹配

tools 也一样。

比如你写：

```yaml
tools:
  - AI 助手
```

但 taxonomy 里真正的工具分类是：

```text
ai编程助手
```

可能会报：

```text
Article "my-new-debug-note" uses unknown tool "AI 助手".
```

这说明文章关联了一个不存在的工具分类。

当前合法工具是：

1. ai编程助手
2. ai IDE/编辑器
3. 版本控制
4. 前端基础
5. 部署发布

所以写文章时，不要自己临时发明分类。

如果确实要新增工具分类，要先改 taxonomy。

但大多数时候，只是 frontmatter 写错了。

---

## 第六类问题：构建通过，但文章打不开

有时候 `npm run build` 通过了，但打开 URL：

```text
/?article=my-new-debug-note
```

页面没有显示文章。

这类问题通常不是 Markdown 文件解析失败，而是 slug 或跳转问题。

要检查：

| 检查点 | 说明 |
|--------|------|
| 注册表里的 slug | 是否写成 `my-new-debug-note` |
| URL 参数 | 是否也是 `my-new-debug-note` |
| 点击入口 | 是否调用了正确 slug |
| 文章是否注册进 articleSources | 是否忘记加入数组 |

注意：

文件名、标题、slug 是三件不同的事。

它们可以相似，但不要混淆。

比如：

```text
文件名：debug-new-article-build-failure.md
标题：新增文章后构建失败：从路径、slug 到 frontmatter 一步步排查
slug：debug-new-article-build-failure
```

URL 用的是 slug，不是标题。

---

## 排查顺序怎么定

遇到新增文章构建失败，可以按这个顺序来：

1. 看错误是不是 `Cannot find module`
2. 如果是，先查文件路径和文件名
3. 看错误是不是 `Cannot find name`
4. 如果是，查 import 变量名和 `raw`
5. 看错误是不是 `invalid frontmatter`
6. 如果是，查 title、stage、tools、updatedAt
7. 看错误是不是 `unknown stage`
8. 如果是，对照 taxonomy 里的阶段名
9. 看错误是不是 `unknown tool`
10. 如果是，对照 taxonomy 里的工具名
11. 如果构建通过但页面打不开，查 slug 和 URL

这就是调试里的"分支判断"。

你不需要一开始就知道答案。

你只需要根据错误信息，一步步缩小范围。

---

## 给 AI 的具体提问方式

如果你遇到这个问题，可以这样问 AI：

```text
我新增文章后 npm run build 失败。

最近改动：
1. 新增 src/content/articles/my-new-debug-note.md
2. 修改 src/data/articles.ts，导入并注册这篇文章

错误信息：
[粘贴完整错误]

请你按下面顺序帮我判断：
1. 是否是 Markdown 文件路径问题
2. 是否是 import 变量名问题
3. 是否是 frontmatter 缺字段
4. 是否是 stage / tools 名称不匹配
5. 如果要修改，只给出最小修改方案
```

这个提示词的好处是：

- 告诉 AI 最近做了什么
- 给出完整错误
- 限定排查范围
- 要求最小修改

这样 AI 更容易像一个调试搭档，而不是一上来重写一大片代码。

---

## 一个完整示例

假设错误是：

```text
Article "my-new-debug-note" uses unknown stage "调试".
```

我们可以这样分析：

1. 文件能被导入，说明路径大概率没问题
2. 文章能被解析，说明 frontmatter 基本存在
3. 错误明确说 unknown stage
4. `"调试"` 不在 taxonomy 的合法阶段里
5. taxonomy 里应该是 `"调试篇"`

最小修改就是：

```yaml
stage: 调试篇
```

然后重新运行：

```bash
npm run build
```

如果通过，再打开文章 URL 验证。

这就是一次完整的调试闭环。

---

## 常见问题

### 1. 文件名必须和 slug 一样吗？

不必须，但强烈建议保持一致。

这样排查问题更容易。

比如：

```text
文件名：debug-new-article-build-failure.md
slug：debug-new-article-build-failure
```

如果文件名和 slug 差太多，以后会很难找。

### 2. 标题可以和 slug 不一样吗？

可以。

标题是给用户看的，可以用中文、冒号和更自然的表达。

slug 是给 URL 和代码用的，建议用英文小写和连字符。

### 3. tools 可以为空吗？

当前项目不建议为空。

因为 Tool Index 依赖它来归类文章。

如果一篇文章真的不确定工具分类，至少可以先标记最相关的一类。

---

## 小结

这一篇用"新增文章后构建失败"练习了一次具体调试。

你需要记住的不是某个固定答案，而是排查顺序：

1. 找不到模块，先查文件路径和文件名
2. 找不到变量，查 import 名称和 `raw`
3. frontmatter 无效，查 title、stage、tools、updatedAt
4. unknown stage，查阶段名称是否和 taxonomy 完全一致
5. unknown tool，查工具名称是否和 taxonomy 完全一致
6. 构建通过但页面打不开，查 slug、URL 和注册表
7. 每次只改一个最可疑点
8. 修改后运行 `npm run build` 验证

调试的关键不是一下子猜中。

而是让每一步都有根据。

> **错误信息不是敌人，它是在告诉你应该从哪里开始看。**

下一篇，我们可以继续练习另一个常见问题：页面白屏但构建通过时，如何用浏览器 Console 和最近改动定位运行时错误。

---

*从零开始学AI编程 · 阶段五 · 调试篇*
