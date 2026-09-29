---
title: 使用 GitHub Pages + Hexo + NexT 搭建个人博客
date: 2026-09-29 14:00:00
tags:
  - Hexo
  - NexT
  - GitHub Pages
  - AI 编程
  - Trae
  - 人机协作
categories:
  - 技术
  - 博客搭建
---

本文记录如何从零搭建一个完全免费的个人博客：使用 **GitHub Pages** 托管、**Hexo** 生成静态页面、**NexT** 作为主题，并通过 GitHub Actions 实现推送即部署。每一步都写清楚了"做什么"和"为什么"，新手照着做就能成功。

## 一、准备工作

需要安装以下工具：

- [Git](https://git-scm.com/)
- [Node.js](https://nodejs.org/)（建议 20 LTS 及以上）
- 一个 GitHub 账号

安装完成后，打开终端（Windows 用 PowerShell）输入 `node -v` 和 `git --version`，能显示版本号说明装好了。

## 二、创建 GitHub 仓库

在 GitHub 上新建一个仓库，名称必须是：

```
用户名.github.io
```

例如我的用户名是 `LingShy`，仓库名就是 `LingShy.github.io`。创建后，`https://lingshy.github.io` 就是博客的访问地址。

创建时**不要**勾选 README、.gitignore 等初始化选项，保持空仓库即可。

## 三、初始化 Hexo

Hexo 是一个静态博客生成器：你用 Markdown 写文章，它帮你生成一整套 HTML 页面。

在本地选好目录，逐条执行以下命令（`#` 后面是注释，不用输入）：

```bash
mkdir LingShy.github.io   # 创建站点文件夹
cd LingShy.github.io      # 进入这个文件夹
npm init -y               # 生成 package.json（Node 项目的依赖清单）
npm install hexo          # 把 Hexo 安装到项目里
npx hexo init .           # 在当前文件夹生成博客的基础结构
npm install               # 安装 hexo init 带出来的其他依赖
```

> `npx` 的意思是"临时调用本项目里安装过的命令"，不需要把 Hexo 全局安装到电脑上。

初始化完成后，目录里会多出这些东西：

| 文件 / 目录 | 作用 |
| --- | --- |
| `_config.yml` | 站点总配置（标题、语言、链接格式等都在这里改） |
| `scaffolds/` | 文章模板，新建文章时按模板生成 |
| `source/_posts/` | 你的博客文章，全部是 Markdown 文件 |
| `package.json` | 依赖清单，记录项目用了哪些包 |

**验证**：执行 `npx hexo server`，浏览器打开 `http://localhost:4000`，看到一篇默认的 Hello World 文章就说明成功了。按 `Ctrl + C` 停止预览。

> 如果提示端口被占用，换一个端口：`npx hexo server -p 5000`。

## 四、安装 NexT 主题

如果说 Hexo 是博客的"发动机"，主题就是"外壳"，决定博客长什么样。NexT 是使用最广泛的 Hexo 主题之一，简洁、文档齐全、持续维护。

NexT 支持通过 npm 安装，好处是**不需要下载到 themes 目录**，以后升级只需一条 `npm update` 命令：

```bash
npm install hexo-theme-next
```

安装完成后，包位于 `node_modules/hexo-theme-next/`，不用手动改动里面的任何文件。

然后在站点根目录的 `_config.yml` 里启用主题：

```yaml
theme: next
```

**验证**：执行下面的命令后刷新 `http://localhost:4000`，界面变成黑白简洁风格就说明主题生效了：

```bash
npx hexo clean    # 清除旧缓存（换主题、改配置后都建议先执行）
npx hexo server
```

## 五、站点配置

打开 `_config.yml`，找到最上面的 `# Site` 部分，把默认值改成自己的信息：

```yaml
# Site
title: LingShy 的个人博客   # 站点名称，显示在浏览器标签页和页面上
subtitle: ''                # 副标题，可以留空
description: ''             # 站点描述，给搜索引擎看（SEO）
author: LingShy             # 作者名，显示在页脚和文章作者处
language: zh-CN             # 界面语言，设为 zh-CN 整个网站自动变成中文

# URL
url: https://lingshy.github.io   # 必须改成你自己的 GitHub Pages 地址
```

几个新手容易踩的坑：

1. **冒号后面必须有一个空格**，例如 `title: 博客`，写成 `title:博客` 会报错。
2. **`url` 一定要填自己的地址**（本地是 `用户名.github.io` 就填 `https://用户名.github.io`），否则生成的文章链接、RSS 都是错的。
3. `language` 大小写写成 `zh-CN` 与主题翻译文件名保持一致，最稳妥。

## 六、配置 NexT 菜单

NexT 默认菜单是空的，需要自己配置导航：首页、分类、标签、归档、关于。

### 6.1 创建主题覆盖配置

**不要直接修改** `node_modules` 里的主题文件——升级主题时改动会全部丢失。Hexo 支持在站点根目录创建 `_config.next.yml`，只写想修改的部分，其余自动使用主题默认值：

```yaml
menu:
  home: / || fa fa-home
  categories: /categories/ || fa fa-th
  tags: /tags/ || fa fa-tags
  archives: /archives/ || fa fa-archive
  about: /about/ || fa fa-user
```

每一行的格式是 `菜单项: 页面地址 || 图标`：

- `home: /` 表示"首页"指向站点根目录
- `||` 后面的 `fa fa-home` 是菜单旁显示的小图标（来自 Font Awesome），不想要图标可以留空
- 菜单的**文字不需要手动写中文**，配置好语言后会自动显示为：首页 / 分类 / 标签 / 归档 / 关于

### 6.2 创建菜单对应的页面

`分类`、`标签`、`关于` 这三个页面还不存在，需要创建：

```bash
npx hexo new page tags        # 生成 source/tags/index.md
npx hexo new page categories  # 生成 source/categories/index.md
npx hexo new page about       # 生成 source/about/index.md
```

然后编辑 `source/tags/index.md`，在 front-matter（文件开头两条 `---` 之间的部分）加上 `type: tags`：

```yaml
---
date: 2026-09-29 12:00:00
type: tags
---
```

`type: tags` 是告诉 NexT："这个页面是标签云页面，请自动列出所有标签"。分类页同理，改成 `type: categories`。

`source/about/index.md` 就是普通的关于页，直接在里面写自我介绍即可（Markdown 格式）。

**验证**：`npx hexo clean && npx hexo server`，菜单出现五项，点击标签页能看到所有标签的云图。

## 七、GitHub Actions 自动部署

到目前为止博客只能在本地看。GitHub Actions 是 GitHub 提供的免费自动化机器人：你告诉它"每次我推送代码，就执行这些步骤"，它就会在自己的服务器上自动构建并把博客发布到 GitHub Pages。

### 7.1 创建工作流文件

在项目里创建 `.github/workflows/pages.yml`（目录不存在就手动新建）：

```yaml
name: Pages
on:
  push:
    branches:
      - master        # 推送到 master 分支时触发

jobs:
  build:              # 第一个任务：构建
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4        # 拉取仓库代码
      - uses: actions/setup-node@v4      # 安装 Node.js 20
        with:
          node-version: '20'
      - run: npm install                 # 安装依赖
      - run: npm run build               # 执行 hexo generate，产物在 public/
      - uses: actions/upload-pages-artifact@v3
        with:
          path: ./public                 # 把 public/ 打包上传
  deploy:             # 第二个任务：发布
    needs: build                    # 等构建完成后再执行
    permissions:
      pages: write                  # 允许写入 GitHub Pages
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/deploy-pages@v4   # 发布到 GitHub Pages
```

### 7.2 开启 GitHub Pages

到仓库页面：**Settings → Pages → Build and deployment → Source**，选择 **GitHub Actions**（默认可能是 "Deploy from a branch"，必须手动改过来）。

### 7.3 推送并查看结果

```bash
git add .
git commit -m "init: hexo blog"
git push
```

推送到 `master` 分支后，打开仓库的 **Actions** 标签页，能看到工作流正在运行：绿勾表示成功（首次大约 1~2 分钟），红叉表示失败，点进去能看到错误日志。成功后访问 `https://用户名.github.io`，博客就上线了。

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

新建的文章在 `source/_posts/` 下，用任何编辑器写 Markdown 都行。推送后等待 Actions 跑完，刷新 `https://lingshy.github.io` 就能看到新文章。

## 九、这次搭建 AI 帮了大忙

### 9.1 Trae 是什么

[Trae](https://www.trae.ai/) 是字节跳动推出的 AI 原生 IDE（国内版官网：[trae.com.cn](https://www.trae.com.cn/)），简单说就是一个"内置 AI 助手的编程工具"。它基于 VS Code 魔改，界面和操作习惯与 VS Code 几乎一致，原有的插件、主题、快捷键都能直接沿用，迁移成本为零。

它的核心能力：

- **Builder 构建模式**：用一句自然语言描述需求，AI 自动完成"读代码 → 改文件 → 跑命令 → 修报错"的完整闭环，适合从零搭建项目
- **Chat 对话模式**：侧边栏随时提问，可以 `#` 引用具体文件、文件夹甚至网页作为上下文，AI 的回答更精准
- **内联编辑与补全**：光标处 `Ctrl+I` 直接让 AI 改写选中代码，日常写码的润色、重构非常顺手
- **Agent / MCP 扩展**：支持接入 MCP 工具（如飞书、浏览器自动化），AI 能力可以延伸到写代码之外

### 9.2 它在这个项目里做了什么

这次博客搭建全程在 Trae 里完成，它帮了大忙：

- **从零生成站点骨架**：`_config.yml`、`package.json`、`scaffolds/` 模板等都是它参考旧博客结构生成的，直接可用
- **主题选型与配置**：推荐了通过 npm 安装 NexT（不用折腾 themes 目录），并完成了菜单中文、标签页、分类页、关于页的整套配置
- **编写 CI/CD 工作流**：GitHub Actions 的 `pages.yml` 由它生成，还逐行注释解释了每个步骤的作用
- **迁移旧文章**：把旧博客的文章和图片搬过来，自动把失效的相对路径图片改成根路径，还把旧用户名的外链全部替换成新地址
- **排查问题**：比如"界面变成德语"这种奇怪现象，它排查后发现其实是浏览器自动翻译，而不是配置错误
- **撰写本文**：你现在读到的这篇文章也是它起草、我审核修改的

### 9.3 为什么推荐

以前搭建这样的博客，需要翻 Hexo 文档、主题文档、GitHub Actions 文档，踩一堆配置的坑，一晚上能跑起来就算顺利。这次整个过程不到一小时，我只负责提需求、验收效果和理解原理，体力活全交给了 AI。

**推荐理由**：如果你是新手，它把"看不懂文档"的门槛降到了"会描述需求"；如果你是老手，它替你写配置、写样板代码、查问题，省下的时间用来思考设计。国内版开箱即用、对中文开发者友好，值得一试。

体会是：这类"步骤多、文档散、纯体力"的工程，AI 适合打下手甚至主刀，人负责提需求、验收效果和理解原理，效率高很多。

## 总结

整套方案完全免费、无需服务器，写作时只关心 Markdown 文件本身，构建和部署全部交给 GitHub Actions。后续可以继续折腾自定义域名、评论系统（如 giscus）、图片图床等功能。
