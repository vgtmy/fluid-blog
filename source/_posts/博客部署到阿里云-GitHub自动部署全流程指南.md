---
title: 博客部署到阿里云+GitHub自动部署全流程指南
tags:
  - hexo
  - 阿里云
  - GitHub
  - 博客
  - 自动部署
  - CI/CD
  - Https 
categories:
  - 学习心得
index_img: /img/aliyun.jpg
banner_img: /img/ailiyungb.jpg
banner_mask_alpha: 0.6
comment: valine
date: 2025-05-26 15:01:50
---

<!-- 这里是模板内容 -->
将 Hexo 博客部署到阿里云服务器的完整流程如下，我们会从本地部署到服务器、配置域名、开放端口、防火墙、安全组等步骤一步一步来，适合初学者。

---
<!--more-->

# 博客部署到阿里云

## 🧱 一、前提准备

你已经有这些资源：

* 本地 Hexo 博客（可以正常 `hexo s` 预览）
* 一台阿里云服务器（IP 是 `47.111.124.118`）
* 一个域名 `vgtmy.com`（在阿里云买的）

我们将做的事情是：

1. 把本地 Hexo 的静态文件传到服务器
2. 用 Nginx 做网页服务器，部署 Hexo 到公网
3. 配置阿里云服务器的防火墙和安全组
4. 把域名 `vgtmy.com` 解析到你的服务器

---

## 🧰 二、安装和准备环境

### 1. 本地打包博客

在本地 Hexo 根目录下运行：

```bash
hexo clean
hexo g
```

生成好的静态文件会在 `public/` 目录下。

---

### 2. 连接你的阿里云服务器（使用 SSH）

在终端连接服务器：

```bash
ssh root@47.111.124.118
```

（第一次登录会提示是否确认，输入 `yes`）

---

### 3. 安装 Nginx（在服务器上）

```bash
# 更新系统
apt update && apt upgrade -y     # 如果是 Ubuntu 系统
# 安装 Nginx
apt install nginx -y
```

---

## 🚚 三、部署 Hexo 到服务器

### 1. 使用 `scp` 上传 `public/` 文件夹到服务器

在本地终端（Hexo 根目录下）运行：

```bash
scp -r ./public/* root@47.111.124.118:/var/www/html/
```

> `/var/www/html/` 是 Nginx 默认的网站根目录。

---

### 2. 重启 Nginx

在服务器上：

```bash
systemctl restart nginx
```

然后在浏览器访问：

```
http://47.111.124.118
```

你应该就能看到博客了！

---

## 🔐 四、设置阿里云防火墙与安全组

登录阿里云控制台：

### 1. 打开服务器的 80 端口（HTTP）

* 进入【云服务器 ECS】
* 找到你的实例，点击【安全组】
* 点击【配置规则】>【添加安全组规则】

  * 协议类型：TCP
  * 端口范围：80
  * 授权对象：0.0.0.0/0
  * 备注：开放网页访问

保存即可。

---

## 🌐 五、配置域名解析（vgtmy.com）

登录阿里云【域名控制台】：

1. 找到你的域名 `vgtmy.com`
2. 进入【解析】页面，添加解析记录：

| 类型 | 主机记录 | 记录值            | 线路   |
| -- | ---- | -------------- | ---- |
| A  | @    | 47.111.124.118 | 默认线路 |

如果你想让 `www.vgtmy.com` 也能访问：

| 类型 | 主机记录 | 记录值            | 线路   |
| -- | ---- | -------------- | ---- |
| A  | www  | 47.111.124.118 | 默认线路 |

---

## 🧪 六、访问网站

解析通常需要几分钟到一小时不等。

你可以打开浏览器访问：

```
http://vgtmy.com
```

或者：

```
http://www.vgtmy.com
```

就能看到你的博客上线了！

---

# 配置 HTTPS
我们现在来为你的 Hexo 博客配置 **HTTPS（SSL证书）**，让访问 `https://vgtmy.com` 更安全、专业。

---

## 🛠️ 一、申请免费 SSL 证书（阿里云）

1. 打开阿里云控制台：[https://yundun.console.aliyun.com](https://yundun.console.aliyun.com)
2. 左侧进入 **SSL证书管理**
3. 点击【免费证书申请】
4. 填写如下内容：

   * 证书品牌：**阿里云**
   * 域名类型：**单域名**
   * 域名：`vgtmy.com`
5. 选择 DNS 验证或 文件验证（推荐 DNS）

#### ✅ 如果选择 DNS 验证：

* 系统会提示你添加一条解析记录（如 `_dnsauth.vgtmy.com` → 某个字符串）
* 回到域名解析控制台，添加这条记录
* 验证成功后，证书会在十几分钟内签发完成

---

## 💾 二、下载证书并上传到服务器

签发完成后：

1. 在阿里云证书控制台中点击“下载”
2. 选择服务器类型：**Nginx**
3. 下载压缩包（里面有两个文件，可能是 `.pem` 和 `.key`）

#### 然后：

将证书上传到服务器，比如 `/etc/nginx/ssl/` 目录：

```bash
mkdir -p /etc/nginx/ssl
scp yourdomain.pem root@47.111.124.118:/etc/nginx/ssl/
scp yourdomain.key root@47.111.124.118:/etc/nginx/ssl/
```

---

## ⚙️ 三、配置 Nginx 启用 HTTPS

编辑你的 Nginx 配置文件：

```bash
nano /etc/nginx/sites-available/default
```

把内容修改如下（或追加 SSL 配置）：

```nginx
server {
    listen 80;
    server_name vgtmy.com www.vgtmy.com;

    # 自动跳转到 https
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name vgtmy.com www.vgtmy.com;

    ssl_certificate /etc/nginx/ssl/yourdomain.pem;
    ssl_certificate_key /etc/nginx/ssl/yourdomain.key;

    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

请根据你证书文件名替换 `yourdomain.pem` 和 `yourdomain.key`。

---

## 🔄 四、重启 Nginx 服务

```bash
nginx -t     # 检查配置有没有错
systemctl reload nginx
```

---

## ✅ 五、访问测试

现在你可以访问：

```
https://vgtmy.com
```

如果看到小锁🔒图标，说明 HTTPS 配置成功啦！


# 自动续签（Let’s Encrypt）

阿里云免费证书不能自动续期，90天过期。你也可以使用 Let’s Encrypt 来实现自动续期，我可以帮你搭配 certbot 安装。

---

## ✅ 一、使用 Let’s Encrypt 配置 HTTPS（自动续签）

我们将使用工具 [`certbot`](https://certbot.eff.org) 自动申请证书并配置 Nginx。

## 🛠️ 步骤 1：安装 Certbot 工具

在你的阿里云服务器上运行以下命令：

```bash
apt update
apt install certbot python3-certbot-nginx -y
```

---

## 🌐 步骤 2：确保域名已经解析成功

你已经把 `vgtmy.com` 和 `www.vgtmy.com` 指向了你的服务器 `47.111.124.118`，请确认它们已经能通过浏览器访问。

确认成功后继续。

---

## 🔐 步骤 3：使用 Certbot 自动申请证书并配置 Nginx

在服务器上执行：

```bash
certbot --nginx
```

然后它会一步步引导你：

* 选择你要申请证书的域名（输入对应编号）
* 是否重定向 HTTP 到 HTTPS？选择 **2（自动重定向）**

如果你看到这类提示：

```
Congratulations! Your certificate and chain have been saved at...
```

说明证书申请成功并自动配置到了 Nginx。

---

## 🔄 步骤 4：自动续期任务确认

Certbot 默认会自动添加续期任务（通过 `cron` 或 `systemd` 定时执行），你可以手动测试一次是否成功：

```bash
certbot renew --dry-run
```

输出没有报错就是 OK 的，证书将在每 60 天自动更新一次。

---

## ✅ 最终效果验证

现在打开你的域名：

```
https://vgtmy.com
```

你应该可以看到：

* 页面正常加载
* 地址栏出现 🔒 安全锁图标
* 支持 HTTPS，自动跳转 HTTP → HTTPS

---

# GitHub Actions 自动部署
要实现 **Hexo 博客通过 GitHub 自动部署（CI/CD）到你的阿里云服务器**，我们可以通过 GitHub Actions 实现一个完整的自动部署流程：

> 🧠 目标效果是：你只要把博客内容推送到 GitHub，GitHub Actions 就自动构建并将生成的博客上传到你的阿里云服务器，自动上线！

---

## 🧱 一、准备条件

### ✅ 你需要有：

1. Hexo 博客项目已托管到 GitHub，例如 `https://github.com/yourname/hexo-blog`
2. 阿里云服务器的 SSH 登录信息（IP、用户名、密码或密钥）
3. 确保服务器已部署好 Nginx 并指向 `/var/www/html`

---

## 🛠️ 二、在 GitHub 上配置 SSH 密钥（推荐用密钥方式）

### 1. 在你本地生成 SSH 密钥（如果还没有）

```bash
ssh-keygen -t rsa -b 4096 -C "deploy@hexo"
```

一路回车，默认保存在 `~/.ssh/id_rsa` 和 `~/.ssh/id_rsa.pub`

### 2. 把公钥复制到你的服务器

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub root@47.111.124.118
```

现在你应该可以免密登录服务器：

```bash
ssh root@47.111.124.118
```

---

### 3. 把私钥添加到 GitHub 仓库的 Secrets

1. 打开你的 GitHub 仓库页面
2. 点击【Settings】>【Secrets and variables】>【Actions】
3. 新增两个 Secrets：

| Name              | Value                           |
| ----------------- | ------------------------------- |
| `SERVER_SSH_KEY`  | 你本地生成的私钥内容（`~/.ssh/id_rsa` 的内容） |
| `SERVER_HOST`     | `47.111.124.118`                |
| `SERVER_USERNAME` | `root`（或你设置的用户）                 |

---

## 📁 三、添加 GitHub Actions 工作流配置

在你的 Hexo 项目根目录新建文件：

```bash
mkdir -p .github/workflows
nano .github/workflows/deploy.yml
```

填入以下内容：

```yaml
name: Hexo Deploy to Aliyun

on:
  push:
    branches:
      - main   # 根据你的默认分支调整，如是 master 就改成 master

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v3

    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'

    - name: Install dependencies
      run: |
        npm install -g hexo-cli
        npm install

    - name: Generate static files
      run: hexo generate

    - name: Deploy to server via SSH
      uses: appleboy/scp-action@master
      with:
        host: ${{ secrets.SERVER_HOST }}
        username: ${{ secrets.SERVER_USERNAME }}
        key: ${{ secrets.SERVER_SSH_KEY }}
        source: "public/*"
        target: "/var/www/html/"
```

---

## ✅ 四、测试部署流程

1. 将更改推送到 GitHub：

```bash
git add .
git commit -m "Add GitHub Actions deploy"
git push
```

2. 打开 GitHub 仓库，进入【Actions】页面，查看 CI 是否成功执行
3. 等待构建 + 部署成功后，访问你的博客：

```
https://vgtmy.com
```

博客应已自动更新！

---

## 🔁 五、未来如何部署？

你只需在本地写好文章、提交并推送到 GitHub：

```bash
hexo new post "xxx"
# 编辑文章...
git add .
git commit -m "update post"
git push
```

**博客就会自动构建并部署上线**！

---

## 📦 六、可选优化（推荐）

* 支持多环境构建（如测试/正式）
* 压缩图片、清理缓存等自动优化
* 添加构建状态徽章
* 配合 Cloudflare 的缓存刷新自动执行

---

# 优化构建与部署流程
很好！我们接下来来进行 **优化构建与部署流程**，让你的 Hexo 博客部署更智能、高效。主要包括以下几个增强：

---

## ✅ 一、自动压缩优化构建（图片压缩 + 清理）

我们可以在 GitHub Actions 中加入构建优化步骤，让生成的静态文件更轻量、更快。

### 1. 安装优化插件（本地先装一次）

```bash
npm install hexo-neat --save
```

然后在 Hexo 配置文件 `_config.yml` 中添加：

```yaml
neat_html:
  enable: true
  exclude:
neat_css:
  enable: true
  exclude:
neat_js:
  enable: true
  mangle: true
  output:
    beautify: false
```

> 这样会自动压缩 HTML/CSS/JS，大幅减少页面体积。

---

## ✅ 二、自动刷新 Cloudflare CDN 缓存（可选）

如果你启用了 Cloudflare CDN，加速效果会非常好。但部署更新后可能访问的还是缓存的旧页面，因此我们可以通过 GitHub Actions 自动刷新缓存！

### 1. 获取 Cloudflare API Token

* 登录 Cloudflare 控制台
* 进入个人设置 → API Tokens → 创建 Token
* 模板选择 **"Purge Everything"**
* 设置权限：

  * Zone.Zone\:Read
  * Zone.Cache Purge\:Edit
* 限定你当前域名 `vgtmy.com`

生成后保存你的 API Token。

---

### 2. 添加 Secrets 到 GitHub 仓库

前往 GitHub 仓库设置：

| Name           | Value               |
| -------------- | ------------------- |
| `CF_ZONE_ID`   | 你的 Cloudflare 区域 ID |
| `CF_API_TOKEN` | 上面生成的 API Token     |

你可以在 Cloudflare 控制台中找到 Zone ID（在域名首页信息栏里）

---

### 3. 修改 `deploy.yml` 添加刷新 CDN 步骤

```yaml
    - name: Purge Cloudflare Cache
      run: |
        curl -X POST "https://api.cloudflare.com/client/v4/zones/${{ secrets.CF_ZONE_ID }}/purge_cache" \
        -H "Authorization: Bearer ${{ secrets.CF_API_TOKEN }}" \
        -H "Content-Type: application/json" \
        --data '{"purge_everything":true}'
```

> 添加在部署成功之后即可。

---

## ✅ 三、完整部署工作流模板（最终版）

这是一个**完整 CI/CD 工作流文件**，具备构建优化、部署、刷新 Cloudflare 缓存的功能：

```yaml
name: Hexo Deploy

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repo
      uses: actions/checkout@v3

    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'

    - name: Install dependencies
      run: |
        npm install -g hexo-cli
        npm install

    - name: Generate optimized static files
      run: hexo clean && hexo generate

    - name: Deploy to server via SSH
      uses: appleboy/scp-action@master
      with:
        host: ${{ secrets.SERVER_HOST }}
        username: ${{ secrets.SERVER_USERNAME }}
        key: ${{ secrets.SERVER_SSH_KEY }}
        source: "public/*"
        target: "/var/www/html/"

    - name: Purge Cloudflare Cache
      run: |
        curl -X POST "https://api.cloudflare.com/client/v4/zones/${{ secrets.CF_ZONE_ID }}/purge_cache" \
        -H "Authorization: Bearer ${{ secrets.CF_API_TOKEN }}" \
        -H "Content-Type: application/json" \
        --data '{"purge_everything":true}'
```

---

## 🔄 四、现在你拥有的是：

* 💻 一套完整的 Hexo 博客源码在 GitHub 上管理
* ⚙️ 自动构建压缩优化后的静态页面
* 🚀 自动部署到阿里云服务器
* ☁️ 自动刷新 Cloudflare CDN 缓存
* 🔐 支持 HTTPS + 自动续签

---

