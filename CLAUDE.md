# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目是什么

OpenAide News（news.openaide.cc）：每天由 AI 自动生成行业新闻简报的静态站点。内容是纯 Markdown，按「分类文件夹 / 日期文件」组织；站点由 GitHub Pages 用 Jekyll 构建，推送到 `main` 后 `.github/workflows/jekyll-gh-pages.yml` 自动部署。

## 常用命令

```bash
bundle install          # 首次；依赖 github-pages gem，与线上构建环境一致

# 本地构建 / 预览（三个环境变量都必要，见下）
LANG=C.UTF-8 LC_ALL=C.UTF-8 PAGES_REPO_NWO=qiaob/news bundle exec jekyll serve
LANG=C.UTF-8 LC_ALL=C.UTF-8 PAGES_REPO_NWO=qiaob/news bundle exec jekyll build
```

- `LANG/LC_ALL`：老版 SCSS 转换器在非 UTF-8 locale 下构建报错（Invalid US-ASCII character）。
- `PAGES_REPO_NWO`：本地没有 git remote 指向 github.com 时，jekyll-github-metadata 需要它；`_config.yml` 里已显式写死 `baseurl: ""` 和 `repository`，避免构建时调 GitHub API。
- 没有测试和 lint；验证方式就是构建成功 + 浏览器看渲染结果。

## 内容模型（核心约定）

```
<分类文件夹>/YYYY-MM-DD.md   # 每日简报，如 AI/2026-07-14.md
```

- 简报 md **没有 front matter**。能被 Jekyll 渲染依赖 github-pages gem 自带插件：optional-front-matter（无 front matter 也当页面处理）、default-layout（自动套 `default` 布局）、titles-from-headings（首个 H1 变成 page.title）、relative-links。改构建方式时不要破坏这条链。
- 简报正文格式约定：H1 为 `分类名 — YYYY-MM-DD`；第一条为 `## 今日头条：...`；其余条目以 `---` 分隔、标题为独占一段的 `**粗体**`（CSS 靠 `p > strong:only-child` 放大它）。首页与归档页的摘要靠字符串 `今日头条：` 切分提取——改这个措辞会让所有摘要消失（有优雅降级，不会构建失败）。
- README.md 只是仓库说明，已在 `_config.yml` 中 exclude，不参与站点。

## 站点架构

所有页面共享「按文件夹自动发现分类」的模式：用 `site.pages` 过滤 `dir != '/'`、`name contains '.md'`、`name != 'index.md'` 得到全部简报，分类 = 一级目录名。因此**新增新闻类型 = 新建文件夹**，首页、顶部导航自动出现，无需改代码。

- `index.html` — 首页：所有简报按日期倒序分组，每天每分类一张卡片（含当日头条摘要）。
- `_layouts/default.html` — 基础布局：品牌字标（openaide news）、自动生成的分类导航（tab 链到分类归档页，无归档存根时退化为该分类最新一期）、文章页的面包屑 + 正文卡片包装。
- `_layouts/category.html` — 分类归档页布局（继承 default）：从 `page.dir` 推断分类，列出该分类全部期数。
- `AI/index.md`、`Dating-Social-Apps/index.md` — 3 行存根，仅声明 `layout: category`，让 `/<分类>/` 有归档页。新分类想要归档页就复制一份。
- `_data/categories.yml` — 分类展示配置（name/emoji/color/desc），可选；未配置的分类回退为「文件夹名 + 📰 + 灰色」。新增分类的完整步骤写在该文件注释里。
- `assets/style.css` — 全部样式。分类主题色通过 CSS 变量 `--cat` 由模板 inline style 注入；浅色为默认，深色靠 `prefers-color-scheme`；无 JS、无外部字体/CDN（兼顾国内访问）。

## 注意事项

- Liquid 受限于 GitHub Pages 的 Jekyll 3.10：没有 `push` filter、不能用自定义插件；现有代码用「capture 拼字符串再 split」的写法做数组去重，属有意为之。
- 简报页 URL 是 `/AI/2026-07-14.html`（默认 permalink）。简报末尾的 `[← 返回首页](../)` 依赖这个相对路径；不要改成 pretty permalink，会把 `../` 指向分类目录。
- 每日生成流程只需产出新的 `YYYY-MM-DD.md`；README 的归档列表已无需维护（更新它也无害）。
