# 光阴的故事 - Fluid Blog

这是“二郎神表弟”的个人博客仓库，基于 [Hexo](https://hexo.io/) 静态站点生成器和 [Fluid](https://github.com/fluid-dev/hexo-theme-fluid) 主题构建。

> 在时光中行走，于故事中停留。

## 🚀 核心技术栈

- **框架**: [Hexo](https://hexo.io/)
- **主题**: [Hexo Theme Fluid](https://github.com/fluid-dev/hexo-theme-fluid)
- **部署**: GitHub Pages / 阿里云 Git 钩子

## 🛠️ 本地开发指南

在本地进行预览和编写文章，请确保已安装 Node.js 和 Hexo-cli。

### 1. 安装依赖

```bash
npm install
```

### 2. 预览博客

运行本地服务器查看效果：

```bash
npm run server
# 或使用 hexo server
```

访问地址通常为：`http://localhost:4000`

### 3. 创建新文章

```bash
hexo new "文章标题"
```

文章将生成在 `source/_posts/` 目录下。

### 4. 清理与构建

```bash
npm run clean   # 清理缓存
npm run build   # 生成静态文件 (public 目录)
```

## 📂 项目结构简述

- `source/_posts/`: 存放 Markdown 格式的文章源码。
- `source/img/`: 存放博客引用的图片资源。
- `_config.yml`: Hexo 站点配置文件。
- `_config.fluid.yml`: Fluid 主题详细配置文件。
- `themes/fluid/`: Fluid 主题目录。

## 🚢 部署说明

本项目配置了双向部署支持：

1. **GitHub Pages**: 推送到 `main` 分支。
2. **私有 Git 部署**: 已在 `_config.yml` 中配置部署到阿里云 Git 指向的目标目录。

使用以下命令进行自动部署：

```bash
npm run deploy
# 或使用 hexo deploy
```

---

*Powered by Antigravity & Hexo*
