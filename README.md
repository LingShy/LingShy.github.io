# LingShy.github.io

基于 [Hexo](https://hexo.io/) 的个人博客，使用 [NexT](https://theme-next.js.org/) 主题，托管于 GitHub Pages，访问地址：[https://lingshy.github.io](https://lingshy.github.io)

## 环境要求

- [Node.js](https://nodejs.org/) >= 20
- Git

## 快速开始

```bash
# 安装依赖
npm install

# 本地预览（默认 http://localhost:4000）
npx hexo server

# 创建新文章（生成到 source/_posts/）
npx hexo new "文章标题"

# 创建草稿
npx hexo new draft "草稿标题"

# 清理缓存
npx hexo clean

# 生成静态文件（输出到 public/）
npx hexo generate
```

## 目录结构

```
├── _config.yml            # Hexo 站点配置
├── package.json           # 依赖管理
├── scaffolds/             # 文章模板（post / draft / page）
├── source/
│   └── _posts/            # 博客文章（Markdown）
└── .github/
    └── workflows/pages.yml  # GitHub Actions 自动部署
```

## 写作流程

1. 新建文章：`npx hexo new "我的新文章"`
2. 编辑 `source/_posts/我的新文章.md`（支持 Markdown）
3. 本地预览：`npx hexo server`
4. 确认无误后提交并推送：

```bash
git add .
git commit -m "post: 新文章标题"
git push
```

## 部署说明

推送代码到 `master` 分支后，[GitHub Actions](.github/workflows/pages.yml) 会自动执行：

1. 安装依赖
2. `hexo generate` 构建静态页面
3. 部署到 GitHub Pages

无需手动构建，推送即发布。

## 自定义

- **站点信息**（标题、作者、语言等）：编辑 [_config.yml](_config.yml) 的 `Site` 部分（语言已设为 `zh-CN`，界面菜单自动显示中文：首页 / 分类 / 标签 / 归档 / 关于）
- **主题**：使用 [NexT](https://github.com/next-theme/hexo-theme-next) 主题，通过 npm 包 `hexo-theme-next` 安装，无需 themes 目录
- **菜单**：在 [_config.next.yml](_config.next.yml) 的 `menu` 中增删菜单项
- **标签 / 分类页**：对应 `source/tags/`、`source/categories/`（front-matter 中 `type: tags`、`type: categories`），`source/about/` 为关于页，可自行编辑
- **主题配置**：在项目根目录创建 `_config.next.yml` 即可覆盖主题默认配置（如切换 Scheme：`scheme: Pisces`）
- **每页文章数、URL 格式**：见 `_config.yml` 中 `index_generator`、`permalink` 配置
