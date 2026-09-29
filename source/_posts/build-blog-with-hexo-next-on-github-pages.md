---
title: 使用 GitHub Pages + Hexo + NexT 搭建个人博客
date: 2026-09-29 14:00:00
tags:
  - Hexo
  - NexT
  - GitHub Pages
categories:
  - 博客搭建
---

本文记录如何从零搭建一个完全免费的个人博客：使用 **GitHub Pages** 托管、**Hexo** 生成静态页面、**NexT** 作为主题，并通过 GitHub Actions 实现推送即部署。

## 一、准备工作

需要安装以下工具：

- [Git](https://git-scm.com/)
- [Node.js](https://nodejs.org/)（建议 20 LTS 及以上）
- 一个 GitHub 账号

## 二、创建 GitHub 仓库

在 GitHub 上新建一个仓库，名称必须是：

```
用户名.github.io
```

例如我的用户名是 `LingShy`，仓库名就是 `LingShy.github.io`。创建后，`https://lingshy.github.io` 就是博客的访问地址。

## 三、初始化 Hexo

在本地选好目录，执行：

```bash
# 创建站点目录并安装依赖
mkdir LingShy.github.io && cd LingShy.github.io
npm init -y
npm install hexo

# 生成基础配置和模板
npx hexo init .
```

也可以手动创建 `package.json`、`_config.yml`、`scaffolds/`、`source/` 等文件，效果一样。

核心目录结构：

```
├── _config.yml            # 站点配置
├── scaffolds/             # 文章模板
├── source/
│   └── _posts/            # 博客文章（Markdown）
└── .github/workflows/     # 自动部署工作流
```

## 四、安装 NexT 主题

NexT 支持通过 npm 安装，不需要下载到 themes 目录，更方便升级：

```bash
npm install hexo-theme-next
```

然后在 `_config.yml` 中启用：

```yaml
theme: next
```

## 五、站点配置

编辑 `_config.yml`：

```yaml
# Site
title: LingShy 的个人博客
author: LingShy
language: zh-CN

# URL
url: https://lingshy.github.io
```

`language: zh-CN` 会让整个界面（菜单、按钮等）显示为中文。

## 六、配置 NexT 菜单

NexT 默认菜单是空的。在站点根目录创建 `_config.next.yml`（Hexo 5+ 的主题覆盖配置，不会改动主题文件），添加菜单：

```yaml
menu:
  home: / || fa fa-home
  categories: /categories/ || fa fa-th
  tags: /tags/ || fa fa-tags
  archives: /archives/ || fa fa-archive
  about: /about/ || fa fa-user
```

菜单文字会根据 `zh-CN` 自动显示为：首页 / 分类 / 标签 / 归档 / 关于。

其中标签页和分类页需要手动创建：

```bash
npx hexo new page tags
npx hexo new page categories
npx hexo new page about
```

然后编辑 `source/tags/index.md`，加上 `type: tags`（分类页同理）：

```yaml
---
date: 2026-09-29 12:00:00
type: tags
---
```

## 七、GitHub Actions 自动部署

在 `.github/workflows/pages.yml` 中创建工作流，推送到 `master` 分支时自动构建并发布：

```yaml
name: Pages
on:
  push:
    branches:
      - master

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm install
      - run: npm run build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: ./public
  deploy:
    needs: build
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/deploy-pages@v4
```

最后到仓库 **Settings → Pages → Source** 选择 **GitHub Actions**，部署即完成。

## 八、日常写作

```bash
# 新建文章
npx hexo new "文章标题"

# 本地预览（http://localhost:4000）
npx hexo server

# 提交推送后自动发布
git add .
git commit -m "post: 新文章"
git push
```

推送后等待 Actions 跑完，刷新 `https://lingshy.github.io` 就能看到新文章。

## 总结

整套方案完全免费、无需服务器，写作时只关心 Markdown 文件本身，构建和部署全部交给 GitHub Actions。后续可以继续折腾自定义域名、评论系统（如 giscus）、图片图床等功能。
