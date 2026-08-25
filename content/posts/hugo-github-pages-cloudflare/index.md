---
title: "用 Hugo + GitHub Pages + Cloudflare 搭建免费个人博客"
date: 2026-08-25T17:30:00+08:00
draft: false
summary: "从零开始，用 Hugo 生成静态网站，通过 GitHub Actions 自动部署到 GitHub Pages，再用 Cloudflare 加速。完全免费。"
tags: ["Hugo", "GitHub Pages", "Cloudflare", "CI/CD"]
categories: ["技术"]
---

## 整体架构

```
本地写 Markdown → Git Push → GitHub Actions 构建 Hugo → GitHub Pages 托管 → Cloudflare CDN 加速 → 用户访问
```

整个链路完全免费，适合个人博客。

## 1. 准备工作

### 1.1 域名
在域名注册商购买域名，将 DNS 托管到 Cloudflare。

### 1.2 GitHub 仓库
创建一个公开仓库，用于存放博客源码。

### 1.3 Cloudflare DNS 配置
在 Cloudflare DNS 面板添加 CNAME 记录：

```
类型: CNAME
名称: @ (或 www)
目标: yourusername.github.io
代理: 开启 (橙色云朵)
```

## 2. Hugo 本地构建

### 2.1 安装 Hugo

```bash
# Windows (winget)
winget install Hugo.Hugo.Extended

# macOS
brew install hugo

# Linux
sudo apt install hugo
```

### 2.2 创建站点

```bash
hugo new site blog
cd blog
git init
git submodule add https://github.com/adityatelange/hugo-PaperMod themes/PaperMod.git
```

### 2.3 写文章

```bash
hugo new content posts/my-first-post/index.md
```

编辑 Markdown 文件，写入内容即可。

### 2.4 本地预览

```bash
hugo server -D
# 打开 http://localhost:1313
```

## 3. GitHub Actions 自动部署

在仓库根目录创建 `.github/workflows/hugo.yml`：

```yaml
name: Deploy Hugo to GitHub Pages

on:
  push:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.165.0
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O hugo_extended.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb
          sudo dpkg -i hugo_extended.deb
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: true
          fetch-depth: 0
      - name: Setup Pages
        uses: actions/configure-pages@v5
      - name: Build with Hugo
        run: hugo --minify
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

## 4. GitHub Pages 配置

1. 进入仓库 **Settings → Pages**
2. **Source** 选择 **GitHub Actions**
3. 推送代码后，Action 会自动构建部署

## 5. 绑定自定义域名

### 5.1 GitHub 侧
在仓库根目录创建 `static/CNAME` 文件，写入你的域名：
```
www.yourdomain.com
```

### 5.2 Cloudflare 侧
确保 DNS 记录的 CNAME 指向 `yourusername.github.io`。

### 5.3 SSL 配置
- Cloudflare SSL/TLS 模式设为 **Full**
- 可开启 **Always Use HTTPS**
- 可开启 **Automatic HTTPS Rewrites**

## 总结

| 步骤 | 工具 | 费用 |
|------|------|------|
| 域名 | 注册商 | 约 ¥50-100/年 |
| DNS/CDN | Cloudflare | 免费 |
| 代码托管 | GitHub | 免费 |
| 自动部署 | GitHub Actions | 免费 |
| 站点托管 | GitHub Pages | 免费 |

除了域名费用外，整个方案完全免费。而且 Cloudflare 的 CDN 能让全球访问都很快。
