---
title: "Hugo 常用命令速查"
date: 2026-08-25T18:00:00+08:00
draft: false
summary: "Hugo 日常使用最常用的命令汇总，写博客必备。"
tags: ["Hugo", "速查表"]
categories: ["技术"]
---

## 站点管理

```bash
# 创建新站点
hugo new site myblog

# 本地预览 (包含草稿)
hugo server -D

# 本地预览 (仅正式文章)
hugo server

# 构建静态文件到 public/
hugo

# 构建并压缩
hugo --minify

# 构建并指定 baseURL
hugo --baseURL https://example.com/
```

## 内容创建

```bash
# 创建新文章
hugo new content posts/my-post/index.md

# 创建 about 页面
hugo new content about.md

# 指定内容类型
hugo new content posts/my-post/index.md --kind post
```

## Front Matter 说明

```yaml
---
title: "文章标题"
date: 2026-08-25T17:00:00+08:00
draft: false           # true = 草稿, false = 正式发布
summary: "摘要"        # 列表页显示的摘要
tags: ["标签1", "标签2"]
categories: ["分类"]
cover:
  image: "images/cover.jpg"
  alt: "封面图片描述"
weight: 1              # 排序权重 (数字越小越靠前)
---
```

## 常用技巧

### 文章中插入图片

将图片放在文章同目录下的 `images/` 文件夹，或直接放在文章目录：

```markdown
![描述](images/photo.jpg)
```

### 使用短代码

```markdown
{{</* figure src="images/photo.jpg" title="图片标题" */>}}
{{</* youtube video_id */>}}
{{</* gist username gist_id */>}}
```

### 内部链接

```markdown
{{</* ref "posts/other-post/index.md" */>}}
```

## 快速发布流程

```bash
# 1. 写文章
hugo new content posts/new-post/index.md

# 2. 本地预览
hugo server -D

# 3. 修改 front matter: draft: false

# 4. 提交推送
git add .
git commit -m "发布新文章: 文章标题"
git push

# 5. GitHub Actions 自动构建部署 ✅
```

---

> 建议: 把 `hugo server -D` 长期开着，写完文章后改 `draft: false`，提交推送即可。
