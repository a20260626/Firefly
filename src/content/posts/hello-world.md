---
title: "Hello World"
published: 2026-07-02T15:26:12+08:00
updated: 2026-07-02T15:26:12+08:00
pinned: false
description: "这是一篇用于验证 M1 项目基础链路的 Markdown 文章。"
tags: ["AI编程", "ai编程助手", "前端基础"]
category: "认知篇"
draft: false
---

## 第一篇占位文章

这是一篇用于验证 M1 项目基础链路的 Markdown 文章。

当前目标不是完成正式教程样式，而是确认静态站已经可以读取本地 Markdown 文件、解析基础元数据，并把文章内容展示到页面上。

## 验证内容

- Vite + React + TypeScript 项目可以启动。
- 本地 Markdown 文件可以被导入。
- frontmatter 可以被解析为文章元数据。
- 正文内容可以在页面中显示。

### GFM 表格

| 能力 | 当前状态 | 说明 |
|:--|:--|:--|
| Markdown 渲染 | 已接入 | 使用 React Markdown |
| 表格 | 已接入 | 由 remark-gfm 支持 |
| 代码复制 | 已接入 | 每个代码块右上角有复制按钮 |

### 代码块

```ts
const site = {
  name: 'AI 编程学习笔记站',
  stage: 'M4 文章阅读体验',
  canCopyCode: true,
};

console.log(`${site.name}: ${site.stage}`);
```

### 踩坑提示

> 踩坑：如果标题锚点被固定工具栏遮住，需要给页面设置 `scroll-padding-top`，并给阅读区标题设置 `scroll-margin-top`。
