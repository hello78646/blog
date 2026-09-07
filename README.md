# 我的技术学习博客

记录自己在 **C 语言、数据结构、算法** 学习路上的进步与每个知识点的理解。

博客地址：<https://hello78646.github.io/blog/>

## 项目结构

```
.
├── _config.yml        # 站点配置（标题、分类名、构建配置）
├── _layouts/          # 页面布局（default / page / post）
├── _posts/            # 文章目录，命名为 YYYY-MM-DD-标题.md
├── assets/css/        # 站点样式
├── c-basics.html      # 分区：C 语言基础
├── data-structures.html  # 分区：数据结构
├── algorithms.html    # 分区：算法
├── index.html         # 首页（三大分区入口 + 最新文章）
├── about.md           # 关于页
└── resources/         # 图片/视频素材（按知识点归类的目录，可自行创建）
```

## 如何发布一篇新文章

1. 在 `_posts/` 新建文件，命名为 `YYYY-MM-DD-标题.md`。
2. 文件顶部写 front matter：
   ```yaml
   ---
   layout: post
   title: 文章标题
   date: 2026-09-07 00:00:00 +0800
   categories: [c-basics]   # 或 data-structures / algorithms
   tags: [标签1, 标签2]
   ---
   ```
3. 添加本地 commit 并 `push` 到 `main` 分支，GitHub Pages 会自动构建上线。

### 分类标识

| 分区 | categories 标识 | 页面 |
|------|----------------|------|
| C 语言基础 | `c-basics` | `/blog/c-basics/` |
| 数据结构 | `data-structures` | `/blog/data-structures/` |
| 算法 | `algorithms` | `/blog/algorithms/` |

### 图片 / 视频

- 图片：放入 `resources/images/{知识点}/`，正文用相对路径 `![描述](../resources/images/xxx/1.png)`。
- 本地视频：放入 `resources/videos/`，用 HTML5 `<video>` 标签引用。
- 外链视频（B站 / YouTube）：用 `iframe` 嵌入对应播放器地址。