# XKCQW 传奇发布网站 · 静态单页站点

一套可直接部署到 **GitHub Pages**（`zhaofuz.github.io`）的静态单页网站：一个 `index.html`
里装下首页与 24 篇原创长文，双击就能在本地打开，推到仓库就能上线。

- 首页标题：`传奇发布网站_新开传奇版本资讯与攻略大全-传奇发布网XKCQW`
- 核心词：**传奇发布网站**（另有 传奇发布网 / 新开传奇 / 传奇版本 / 三职业攻略 等长尾）
- 内容规模：6 个分类、24 篇文章、约 1.66 万字正文
- 体积：`index.html` 269 KB（gzip 后约 58 KB），整站 420 KB，无外部字体、无 CDN、无图片依赖
- 结构：单页 + hash 路由（`#/post/<slug>`），全部正文**预渲染在 HTML 源码里**，不靠 JS 才有内容

---

## 一、目录结构

```
zhaofuz.github.io/
├── index.html                 ← 构建产物：首页 + 24 篇文章正文（部署必需）
├── 404.html                   GitHub Pages 的 404 页面
├── robots.txt                 爬虫协议（放行百度、指向 sitemap）
├── sitemap.xml                站点地图
├── site.webmanifest           PWA 清单
├── .nojekyll                  告诉 GitHub Pages 不要用 Jekyll 处理
├── assets/
│   ├── css/site.css           全站样式（深色 + 鎏金，移动端优先）
│   ├── js/site.js             路由、筛选、搜索、阅读进度、复制链接
│   └── img/
│       ├── favicon.svg        站点图标
│       ├── apple-touch-icon.png
│       └── og-cover.png       分享封面 1200×630（也是结构化数据里的 image）
└── _src/                      源码目录（不参与站点展示，robots 已屏蔽）
    ├── build.py               构建脚本：Markdown → 单页 HTML + sitemap
    ├── make_assets.py         生成 og-cover 与 iOS 图标（需要 Pillow）
    ├── verify.py              部署前自检：标签、链接、SEO、JSON-LD
    └── content/
        ├── site.json          站点配置：标题、关键词、描述、分类、FAQ、关于、免责声明
        └── articles/*.md      24 篇文章（Markdown + front matter）
```

> 部署时把**仓库根目录**指向这个文件夹的内容即可：`index.html` 必须在仓库最外层。

---

## 二、本地预览

```powershell
# 任选其一，二选一即可
python -m http.server 8080
# 然后浏览器打开 http://localhost:8080/
```

也可以直接双击 `index.html`——所有资源都是相对路径，`file://` 协议下同样可用。

---

## 三、部署到 GitHub Pages

### 1. 创建仓库

仓库名**必须**是 `zhaofuz.github.io`（用户主页仓库），并且选 **Public**。
只有这个名字的仓库，地址才会是 `https://zhaofuz.github.io/`，不用额外配置 `basePath`。

### 2. 上传文件

把本文件夹里的**全部内容**（不是文件夹本身）上传到仓库根目录，最终结构应当是：

```
zhaofuz.github.io/
├── index.html
├── 404.html
├── robots.txt
├── sitemap.xml
├── assets/
└── ...
```

网页端「Add file → Upload files」直接拖进去最省事；有 Git 环境也可以用：

```powershell
git init
git add .
git commit -m "chore: init 传奇发布网站单页站"
git branch -M main
git remote add origin https://github.com/xkcqw/zhaofuz.github.io.git
git push -u origin main
```

### 3. 打开 Pages

仓库 → **Settings → Pages** → Source 选 **Deploy from a branch** → Branch 选 **main**、目录选 **/(root)** → Save。

1～2 分钟后访问 `https://zhaofuz.github.io/`。首次发布有时要等 5 分钟，属正常现象。

### 4. 两个容易踩的坑

- **不要多套一层文件夹**：如果访问首页是 404，多半是仓库里变成了 `zhaofuz.github.io/zhaofuz.github.io/index.html`。
- **不要删 `.nojekyll`**：否则 GitHub 的 Jekyll 会忽略以下划线开头的目录，也容易改动输出。

---

## 四、日常维护

### 新增一篇文章

1. 在 `_src/content/articles/` 下新建 `my-new-post.md`（文件名就是 URL 里的 slug，建议用英文短横线）。
2. 写 front matter + 正文：

```markdown
---
title: 文章标题（20 字内更利于搜索结果展示）
category: wanfa          # 必须是 site.json 里已有的分类 slug
date: 2026-11-10         # 用于排序，格式 YYYY-MM-DD
tags: 打宝,效率,新区      # 3 个以上，用英文逗号分隔
excerpt: 一句话摘要，卡片和 description 会用到，建议 40～70 字。
---

正文第一段（会显示在文章顶部）。

## 小节标题

段落内容。支持 **加粗**、`行内代码`、[站内链接](#/post/slug)。

- 无序列表
- 第二项

> 引用块会渲染成金色提示框，适合放结论。

1. 有序列表
2. 第二项
```

3. 运行构建：

```powershell
python _src/build.py      # 重新生成 index.html 与 sitemap.xml
python _src/verify.py     # 自检，全部通过再上传
```

### 分类 slug 对照

| slug | 分类名 | 建议内容 |
| --- | --- | --- |
| `banben` | 版本资讯 | 版本差异、趋势、对比 |
| `kaiqu` | 开区指南 | 开区准备、时间线、新区节奏 |
| `xinshou` | 新手入门 | 建号、升级、术语 |
| `zhiye` | 职业攻略 | 战士 / 法师 / 道士 |
| `wanfa` | 玩法技巧 | 打宝、强化、PK、行会战 |
| `changshi` | 安全与常识 | 防骗、账号安全、版权合规 |

### 修改站点信息

全部集中在 `_src/content/site.json`：

| 字段 | 作用 |
| --- | --- |
| `title` | 首页 `<title>`（核心词已前置，改完记得控制在 30 字内） |
| `keywords` | `<meta name="keywords">` |
| `description` | `<meta name="description">`，建议 80～120 字 |
| `heroEyebrow` / `heroLead` / `heroNote` | 首屏文案 |
| `stats` | 首屏四个数字 |
| `categories` | 分类名称、描述、排序（顺序即导航顺序） |
| `faq` | 常见问题（同时生成 FAQ 结构化数据） |
| `about` | “关于本站”段落 |
| `disclaimer` | 页脚免责声明 |
| `url` | 站点地址，`canonical`、`og:url`、`sitemap` 都跟着它变 |

改完统一跑一次 `python _src/build.py`。

### 修改样式 / 交互

- 颜色、字号、间距：`assets/css/site.css` 顶部的 `:root` 变量。
- 路由、搜索筛选、阅读进度：`assets/js/site.js`（无第三方依赖）。
- 换封面图：`python _src/make_assets.py`（需要 Pillow 与系统中文字体），或直接用同尺寸 PNG 覆盖 `assets/img/og-cover.png`。

---

## 五、命令速查

| 命令 | 作用 |
| --- | --- |
| `python _src/build.py` | 构建 `index.html`、`sitemap.xml`、`site.webmanifest` |
| `python _src/build.py --check` | 只跑内容自检（字数、重复标题、分类归属、核心词覆盖） |
| `python _src/verify.py` | 部署前结构自检：标签闭合、死链、SEO meta、JSON-LD、文件清单 |
| `python _src/make_assets.py` | 重新生成分享封面与 iOS 图标 |

> Windows PowerShell 下如果中文输出乱码：`$env:PYTHONIOENCODING="utf-8"` 再执行。

---

## 六、为百度做的优化（已内置）

| 项目 | 实现方式 |
| --- | --- |
| 标题 | 核心词前置：`传奇发布网站_新开传奇版本资讯与攻略大全-传奇发布网XKCQW`（30 字内） |
| 关键词 / 描述 | `<meta keywords>` 与 100 字左右的 `<meta description>`，核心词自然出现 2 次 |
| 正文可抓取 | 24 篇文章的**完整正文**都在 `index.html` 源码里（`<article hidden>`），爬虫不执行 JS 也能读到 |
| 语义化结构 | `header / nav / main / article / section / aside / footer`，h1→h2→h3 层级，面包屑导航 |
| 结构化数据 | JSON-LD：`WebSite`、`CollectionPage`、`BreadcrumbList`、`ItemList`（24 篇）、`FAQPage` |
| 移动适配 | 响应式 + `<meta name="applicable-device" content="pc,mobile">`、`mobile-agent`、`alternate` |
| 收录入口 | `robots.txt` 放行 Baiduspider；`sitemap.xml` 指向首页；`404.html` 兜底 |
| 速度 | 无外链字体/CDN/图片，CSS 一张表、JS 一个文件（defer），首屏无需请求第三方 |
| 内链 | 分类页、相关阅读 3 篇、上一篇/下一篇、标签云、面包屑 |
| 反作弊 | 无弹窗、无强制跳转、无隐藏关键词、无自动播放 |

### 提交给百度（上线后做一次）

1. 打开 [百度搜索资源平台](https://ziyuan.baidu.com/)，添加站点 `https://zhaofuz.github.io/`。
2. 验证方式选 **HTML 标签**：把平台给的 `codeva-xxxx` 填进 `_src/build.py` 里被注释的
   `<meta name="baidu-site-verification">` 那一行，取消注释后重新构建、上传，再回平台点验证。
3. **普通收录 → sitemap**：提交 `https://zhaofuz.github.io/sitemap.xml`。
4. **普通收录 → API 提交**（可选）：想要更快收录，可在每次更新后手动推送首页 URL。
5. **自动推送**（可选）：`_src/build.py` 末尾已经留好注释版脚本，拿到推送 token 后启用即可。
6. 移动适配：本站是响应式设计，不需要单独提交移动站；在平台里确认「自适应」即可。

### 几个 SEO 上的实话

- 单页只有 **1 个可被独立收录的 URL**，其余内容都在同一个页面里。对“一个关键词 + 一批长文”的站点，
  这样做收录快、维护简单，但**无法让每篇文章各占一个搜索结果位**。
- 如果以后想让每篇文章独立被收录，改造方法见下一节，工作量约半小时。
- 内容质量 > 技术细节。持续每周更新 1～2 篇原创、保持内链，比反复改 title 有效得多。

---

## 七、单页结构说明（以及怎么改成多页）

**路由**：`#/` 首页、`#/post/<slug>` 打开文章、`#/cat/<slug>` 首页内按分类筛选、
`#/tag/<标签>` 按标签筛选、`#faq` / `#about` 跳到首页对应区块。

**为什么正文预渲染**：hash 路由本身不会被搜索引擎当成新页面，所以把 24 篇正文直接写进
`index.html`，用 `hidden` 控制显示——用户看到的是单页体验，爬虫拿到的是完整内容。
同时提供 `<noscript>` 降级：禁用 JS 时全部文章顺序展示，仍可阅读。

**改成多页（可选）**：若想让每篇文章独立收录，在 `build.py` 的 `main()` 里按文章循环输出
`article/<slug>.html`（复用同一套 CSS/JS 与 `render_article` 的片段），把卡片链接从
`#/post/slug` 改成 `../article/slug.html`，并在 `build_sitemap()` 里追加这些 URL。
首页仍可保留列表与摘要。

---

## 八、常见问题

**Q：部署后打开是 404？**
A：① 仓库名不是 `zhaofuz.github.io`；② `index.html` 不在仓库根目录；③ Settings → Pages 没选
`main` / `/(root)`；④ 刚推送还没构建完，等 2 分钟。

**Q：改了文章，网站没变化？**
A：Markdown 不会自动变成 HTML，必须重新执行 `python _src/build.py` 再上传新的 `index.html`；
上传后浏览器按 `Ctrl + F5` 强刷，GitHub Pages 也可能有 1～2 分钟缓存。

**Q：`sitemap.xml` 里为什么只有一个网址？**
A：这是单页站，其它“页面”都是同一份 HTML 的 hash 视图，提交带 `#` 的地址属于重复内容，
对收录没有帮助，所以只提交首页。

**Q：想换成自己的域名？**
A：仓库根目录加一个 `CNAME` 文件，内容写域名；同时把 `_src/content/site.json` 的 `url`
改成新域名，重新构建（`canonical`、`og:url`、`sitemap` 会一起更新）。

**Q：`index.html` 会不会太大？**
A：269 KB，gzip 后约 58 KB，和一张图片差不多。文章增加到 60 篇以上时，建议改成多页结构。

**Q：内容合规吗？**
A：本站只做资讯整理与经验分享：不提供客户端下载、没有充值入口、不承接代练与账号交易，
页脚有明确的免责声明。文章中的数值来自玩家公开讨论，页面也提示以游戏内实际设定为准。

---

## 九、版权

站内文字为原创整理，转载注明来源并保留链接即可，禁止批量采集、洗稿与付费售卖。
游戏名称、商标归各自权利人所有，本站与任何游戏官方、运营方无隶属关系。
