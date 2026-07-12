---
title: "页面白屏但构建通过：用 Console 找到运行时错误"
published: 2026-07-10T16:09:05+08:00
updated: 2026-07-03T16:09:05+08:00
pinned: false
description: "上一篇我们练习了新增文章后构建失败。"
tags: ["AI编程", "ai编程助手", "ai IDE/编辑器", "前端基础"]
category: "调试篇"
draft: false
---

> 构建通过不代表页面一定正常。
> 有些错误只有浏览器真正运行代码时才会出现，这一篇就用"页面白屏但 npm run build 通过"来练习运行时调试。

---

上一篇我们练习了新增文章后构建失败。

那类问题的特点是：终端已经明确报错，`npm run build` 过不去。

但真实开发里还有另一类更迷惑的问题：

> **构建通过了，页面却白屏。**

这时新手很容易困惑：

```text
明明 npm run build 成功了，为什么浏览器还是坏的？
```

答案是：

> 构建检查的是打包和类型层面的错误，浏览器运行时还可能出现新的错误。

这一篇我们就专门讲这种情况。

---

## 什么是运行时错误

运行时错误，就是代码在浏览器里真正执行时才暴露的问题。

举几个例子：

- 访问了 `undefined` 上的属性
- 某个数据为空，但代码假设它一定存在
- 点击事件里调用了不存在的函数
- URL 参数对应不到文章
- 组件渲染时遇到异常
- Markdown 内容结构让某个解析逻辑出错

这些问题可能不会阻止构建。

但用户打开页面时，React 组件一执行，就会报错。

严重时页面直接白屏。

所以遇到白屏时，第一反应不应该是继续改代码。

第一反应应该是：

> **打开浏览器 Console。**

---

## 第一步：确认终端是否真的没报错

页面白屏时，先看终端。

如果 `npm run dev` 正在运行，终端可能会显示一些错误。

如果终端没有明显报错，再看浏览器。

这里要分清两个场景：

| 场景 | 优先看哪里 |
|------|------------|
| `npm run dev` 启动失败 | 终端 |
| `npm run build` 失败 | 终端 |
| 构建通过但页面白屏 | 浏览器 Console |
| 页面能打开但样式错 | 页面元素和 CSS |

不要把所有问题都混在一起。

白屏但构建通过，重点通常在浏览器运行时。

---

## 第二步：打开浏览器 Console

在浏览器里打开开发者工具。

常见方式是：

- 右键页面，选择"检查"
- 或按 `F12`
- 或按 `Ctrl + Shift + I`

然后切到 Console 面板。

如果页面白屏，Console 里通常会有红色错误。

比如：

```text
TypeError: Cannot read properties of undefined (reading 'title')
```

这句话非常关键。

它不是噪音。

它在告诉你：

> 代码试图读取一个不存在对象上的 `title`。

这很可能和文章数据、当前 slug 或空状态处理有关。

---

## 第三步：不要只复制第一行

Console 错误通常有两部分：

1. 错误摘要
2. 调用堆栈

错误摘要告诉你出了什么问题。

调用堆栈告诉你问题大概发生在哪里。

比如：

```text
TypeError: Cannot read properties of undefined (reading 'title')
    at ArticleReader (ArticleReader.tsx:88)
    at App (App.tsx:210)
```

这段信息里有两个关键文件：

- `ArticleReader.tsx`
- `App.tsx`

这就比一句"白屏了"强太多。

你可以把完整错误复制给 AI，让它先判断：

```text
页面白屏，但 npm run build 通过。
Console 报错如下：
[粘贴完整错误和堆栈]

请先解释错误意思，再判断最可能和哪个文件有关。
```

这就是正确打开方式。

---

## 第四步：结合最近改动

运行时错误也要结合最近改动。

比如你刚才改了：

- `src/App.tsx`
- `src/components/ArticleReader.tsx`
- `src/data/articles.ts`

那白屏就大概率和这些文件有关。

如果你刚才只改了 CSS，白屏反而不太像 CSS 问题。

CSS 错了通常是布局难看。

不会让 React 整个渲染崩掉。

所以给 AI 的信息应该包括：

```text
我刚才改了：
1. src/App.tsx 的文章切换逻辑
2. src/components/ArticleReader.tsx 的上一篇 / 下一篇按钮

现在构建通过，但打开文章页白屏。
Console 错误是：
[粘贴错误]
```

这样 AI 就能把错误和最近改动联系起来。

---

## 常见白屏原因一：数据不存在但代码直接读取

最常见的白屏原因是：

> 某个数据可能不存在，但代码直接读取它的属性。

比如：

```ts
const article = articles.find((item) => item.slug === selectedSlug);

return <h1>{article.title}</h1>;
```

如果 `article` 没找到，它就是 `undefined`。

这时读取 `article.title` 就会报错。

更稳的写法是先处理空状态：

```tsx
if (!article) {
  return <p>没有找到这篇文章。</p>;
}

return <h1>{article.title}</h1>;
```

这就是运行时防御。

不是所有数据都一定存在。

尤其是 URL 参数来自用户输入时，更要考虑找不到的情况。

---

## 常见白屏原因二：数组索引越界

上一篇 / 下一篇也容易出运行时问题。

比如：

```ts
const nextArticle = articles[currentIndex + 1];

return <button>{nextArticle.title}</button>;
```

如果当前文章已经是最后一篇，`nextArticle` 就是 `undefined`。

这时读取 `nextArticle.title` 会报错。

正确做法是：

```tsx
{nextArticle ? (
  <button>{nextArticle.title}</button>
) : (
  <button disabled>没有下一篇</button>
)}
```

这类问题的关键词通常也是：

```text
Cannot read properties of undefined
```

看到它，不要慌。

先找是哪一个变量可能是 `undefined`。

---

## 常见白屏原因三：事件处理函数没传进去

组件之间传函数时，也容易出现运行时错误。

比如子组件里调用：

```ts
onArticleNavigate(article.slug);
```

但父组件没有传 `onArticleNavigate`。

Console 可能会出现：

```text
TypeError: onArticleNavigate is not a function
```

这个错误的意思是：

> 代码把一个不是函数的东西当函数调用了。

排查时要看：

1. 子组件 props 里是否声明了这个函数
2. 父组件是否真的传了
3. 这个函数是否可能是可选的
4. 调用前是否需要判断存在

如果这个函数是可选的，可以写成：

```ts
onArticleNavigate?.(article.slug);
```

但更重要的是想清楚：

> 这个组件没有导航函数时，应该还能正常工作吗？

---

## 常见白屏原因四：Markdown 内容触发渲染异常

有时候白屏和代码逻辑有关，也可能和某篇文章内容有关。

比如：

- 图片路径格式异常
- 表格结构很奇怪
- 标题生成 id 时遇到特殊字符
- 代码块语言标记不符合预期

这类问题的判断方式是：

1. 首页是否正常？
2. 只有某一篇文章白屏，还是所有文章都白屏？
3. 打开前一篇文章是否正常？
4. 注释掉最近新增的 Markdown 内容是否恢复？

如果只有某一篇文章白屏，问题可能和这篇文章内容有关。

如果所有文章都白屏，更可能是组件或全局逻辑问题。

这就是缩小范围。

---

## 常见白屏原因五：URL 状态没有同步

本站用 query 参数打开文章。

比如：

```text
/?article=debug-new-article-build-failure
```

如果 URL 更新了，但 React 状态没同步，可能出现：

- URL 看起来变了，正文没变
- 刷新后找不到文章
- selectedArticle 是空
- 上一篇 / 下一篇计算错

这种问题不一定总是白屏，但会让页面状态混乱。

排查时要看：

1. URL 里的 slug 是什么
2. `articles` 里有没有这个 slug
3. 当前 selectedSlug 是否同步
4. selectedArticle 是否能找到
5. 点击文章后是否滚动到顶部

如果 Console 提示 undefined，结合这些状态就很好查。

---

## 给 AI 的白屏调试模板

遇到白屏但构建通过，可以这样问 AI：

```text
页面白屏，但 npm run build 可以通过。

当前 URL：
[粘贴 URL]

最近改动：
[列出刚才修改的文件和功能]

浏览器 Console 错误：
[粘贴完整错误和调用堆栈]

请你：
1. 先解释这个运行时错误是什么意思
2. 根据调用堆栈判断最可能的文件
3. 根据最近改动列出 3 个可能原因
4. 先给出排查步骤，不要直接大改
5. 如果需要修改，只给最小修改方案
6. 修改后告诉我怎么验证
```

这个模板会让 AI 先分析，再动手。

调试时，这比直接说"帮我修白屏"稳很多。

---

## 如何验证白屏修好了

修复后，不要只刷新一次。

至少检查几个路径：

1. 打开首页是否正常
2. 打开当前文章 URL 是否正常
3. 从首页点击文章标题是否正常
4. 点击继续阅读是否正常
5. 点击上一篇 / 下一篇是否正常
6. 打开一个不存在的 slug 是否有合理空状态
7. Console 是否还有红色错误
8. `npm run build` 是否仍然通过

如果只验证当前页面，可能会漏掉别的入口。

比如修好了直接 URL，但从首页点击仍然白屏。

所以要按用户路径走一遍。

---

## 一个完整示例

假设 Console 报错：

```text
TypeError: Cannot read properties of undefined (reading 'title')
    at ArticlePager (ArticleReader.tsx:42)
```

我们可以这样判断：

1. 错误发生在 `ArticlePager`
2. 它在读取某个对象的 `title`
3. `ArticlePager` 可能在显示上一篇 / 下一篇
4. 如果当前是第一篇，previousArticle 可能是 undefined
5. 如果当前是最后一篇，nextArticle 可能是 undefined
6. 代码需要在读取 title 前判断文章是否存在

最小修复方向是：

```tsx
if (!article) {
  return <button disabled>{label}</button>;
}
```

或在渲染时加条件判断。

然后验证：

- 第一篇文章顶部上一篇是否禁用
- 最后一篇文章下一篇是否禁用
- 中间文章前后篇是否正常
- Console 是否无红色错误

这就是一次完整的运行时调试。

---

## 常见问题

### 1. 构建通过为什么还会白屏？

因为构建主要检查打包和类型。

浏览器运行时还会遇到真实数据、真实 URL、真实点击行为。

这些问题可能只有打开页面时才出现。

### 2. Console 里有很多错误，先看哪个？

先看第一条红色错误。

后面的错误可能是第一条错误引发的连锁反应。

如果修复第一条后，后面很多错误可能会一起消失。

### 3. 没有 Console 错误但页面空白怎么办？

那可能不是运行时异常，而是渲染条件、CSS 隐藏、数据为空或路由状态问题。

这时要检查页面 DOM、当前 URL、selectedArticle 是否存在，以及内容是不是被样式隐藏了。

---

## 小结

这一篇练习的是"页面白屏但构建通过"。

你需要记住这条排查线：

1. 先确认终端没有启动或构建错误
2. 打开浏览器 Console
3. 复制完整错误和调用堆栈
4. 结合最近改动判断相关文件
5. 重点查 undefined、数组越界、函数没传、URL 状态不同步
6. 不要一上来大改，先缩小范围
7. 修完后检查首页、文章 URL、点击入口和 Console
8. 最后再跑 `npm run build`

白屏不是神秘问题。

它只是浏览器在告诉你：

> 某段代码运行时遇到了它处理不了的情况。

学会看 Console，你就已经迈过了调试里很重要的一关。

下一篇，我们可以继续看样式类问题：页面没有报错，但正文宽度、表格或代码块把布局撑坏了，应该怎么排查。

---

*从零开始学AI编程 · 阶段五 · 调试篇*
