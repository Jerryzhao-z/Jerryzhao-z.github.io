# Jerry · Astro 基础站点

这是 `jerryzhao.com` 的非破坏性 Astro 重建基础版本。当前改动仅位于 Multica 任务专用分支，尚未推送、部署或修改线上 Pages/DNS 设置。

## 旧仓库审计

- `master` 基线为 `cf31acc`，仓库含 25 个 2021 年 Gridea 生成提交；本次未改写 Git 历史。
- 基线有 49 个静态文件：根目录 HTML、`post/`、`tag/`、`post-images/`、Feed 与单一 CSS。
- 无 `package.json`、构建脚本或 Actions，原发布方式是把 Gridea 产物提交到用户主页仓库根目录。
- `CNAME` 为 `jerryzhao.com`；旧 HTML、Feed 和资源大量写死 GitHub Pages 域名。
- 旧页面从 CDN 加载 KaTeX/highlight.js。旧文章未迁移，仍可从 Git 历史恢复。

## 新结构

- Astro 静态输出，无默认客户端 JavaScript。
- 首页、统一记录列表、关于页与动态记录路由。
- `src/content/posts/` 接受 Markdown/MDX；schema 预留标签、系列、顺序、草稿、精选与更新时间。学习笔记、问题思考、观察和随想暂不拆分频道，以标签区分。
- `remark-math` + `rehype-katex` 支持公式；Astro/Shiki 提供构建时代码高亮。
- Pages 工作流仅监听 `master`；`public/CNAME` 保留域名，但未修改线上配置。

## 本地运行

要求 Node.js `>=22.12.0`，推荐 Node 24 LTS。

```bash
npm install
npm run dev
npm run build
npm run preview
```

新文章放在 `src/content/posts/`，使用 `.md` 或 `.mdx`，frontmatter 示例：

```yaml
title: 文章标题
description: 一句话摘要
publishedAt: 2026-09-03
tags: [概率统计, 基础]
series: 概率论基础
seriesOrder: 1
draft: false
```

两篇内容模型示例均标记为草稿，不进入首页、记录列表或静态路由；正式内容确定后可删除或改写。

## 上线前仍需决定

- 暖纸色与朱红/墨绿视觉是否长期保留。
- 正式头像与社交链接。
- 首批正式记录，以及两篇草稿示例最终删除还是改写。
- 旧文章是否迁移、旧 `/post/.../` URL 是否重定向。
- RSS、搜索、评论、分析和多语言是否需要；基础版本暂不引入。

上线与回滚方案见 `docs/launch-checklist.md`。推送、合并、Pages 设置或 DNS 修改均需单独批准。
