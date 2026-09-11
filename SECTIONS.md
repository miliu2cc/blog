# 栏目说明：nanolog（随手记）与 projects（项目）

首页那部分见 `HOMEPAGE.md`，本文件只讲这两个新增栏目怎么用、怎么加内容、有哪些坑。

---

## 一、目录与 URL 总览

| 内容 | 产物 URL | 模板（主题里自带的） |
| --- | --- | --- |
| 随手记列表 | `/nanolog/`、`/en/nanolog/` | `nanolog_list.html` |
| 随手记单条 | `/nanolog/2026-09-11t17-40-00z/` | `nanolog.html` |
| 项目列表 | `/projects/`、`/en/projects/` | `article_list.html` |
| 项目详情 | `/projects/example-one/`、`/en/projects/example-one/` | `project.html` |

侧边栏导航（`zola.toml` 的 `[extra.nav]` / `[extra.nav.en]`）已经加了三个入口：

```toml
[extra.nav]
links = [
    { url = "@/blog/_index.md", name = "博客", icon = "newspaper" },
    { url = "@/nanolog/_index.md", name = "随手记", icon = "note-pencil" },
    { url = "@/projects/_index.md", name = "项目", icon = "cube" },
]
```

图标名取自 [Phosphor](https://phosphoricons.com)，想换名字改 `icon` 即可。

---

## 二、随手记（nanolog）

### 2.1 栏目配置：`content/nanolog/_index.md`

```toml
+++
title = "随手记"
description = "比微博还短的碎碎念。"
template = "nanolog_list.html"
page_template = "nanolog.html"
sort_by = "date"
generate_feeds = true
[extra]
styles = ["nanolog/style.css"]     # 站点 static/ 下的样式
scripts = ["nanolog/script.js"]    # 发布浮层的脚本
+++
```

英文版 `content/nanolog/_index.en.md` 同结构，只有标题/描述是英文。

### 2.2 加一条：就是一个文件

文件名必须符合 Zola 能解析的日期格式，**日期从文件名来**（所以正文 front matter 可以是空的）：

```
content/nanolog/2026-09-11T17:40:00Z.md
```

```markdown
+++
+++

正文随便写几行，支持 Markdown。
```

- 一条一个文件，建议保持短（发布浮层的 textarea 限 500 字，纯手写不限制）。
- 文件名里的时间会按 `zola.toml` 的 `timezone = "Asia/Shanghai"` 显示，例如 `2026-09-11T17:40:00Z` 在列表里显示为 `2026 年 09 月 12 日 01:40 (CST)`。
- 单条页面自带「上一条 / 下一条」时间轴跳转（`#nanolog-higher` / `#nanolog-lower`）。
- 想要英文版，再写一个同名的 `.en.md`；**不写的话英文列表是空的**（当前就是这样，`/en/nanolog/` 里 0 条）。

### 2.3 ⚠️ 页面右下角那个按钮 = 在浏览器里直接发布

主题的 `nanolog_list.html` 带一个完整的「发布浮层」：右下角圆形按钮 → 打开浮层 → 填内容 → 点 Publish，它会拿你填的 **GitHub token** 调用 GitHub API，把 `.md` 提交到你的仓库。

我做了两件事：
1. **补上了样式** `static/nanolog/style.css`——主题只带了时间轴的样式，浮层的显示/隐藏（`.active`）和内部排版原来缺一半，现在按钮可见、浮层能正常开关（用 headless Chromium 点过，实测 `modal: none → flex`、无 JS 报错）。
2. **给 `static/nanolog/script.js` 加了判空**：这个脚本原本假设页面里有 `#save-token-btn`，而主题模板没有这个元素，会在第 6 行直接抛错、整段失效。加判空后不再崩。

**但你要清楚它的代价**：

- 它需要你在浏览器里粘贴一个 **GitHub Personal Access Token**（写权限），token 存在 `localStorage`；
- 浮层里还有个「Translate」按钮，用的是 **Gemini API key**（同样存 localStorage）；
- `source_url` 必须填对，否则「Edit Post」链接会指错地方（已在 `zola.toml` 里设成 `https://github.com/miliu2cc/blog`）。

**如果不想要前端发布功能**，两种做法：

```css
/* 方案 A：只藏按钮（保留代码，随时能恢复）——static/nanolog/style.css 末尾那两条删掉即可 */
```

方案 B：把 `scripts = ["nanolog/script.js"]` 从 `_index.md` 里去掉，浮层就没法打开了（样式留着也不影响）。这样最干净，也不用在页面上放 token 输入框。

> 我倾向于方案 B——写博客用本地 `zola serve` + git 提交就够了，没必要为了少敲几条命令把写权限 token 放进浏览器。

---

## 三、项目（projects）

### 3.1 栏目配置：`content/projects/_index.md`

```toml
+++
title = "项目"
sort_by = "weight"              # 按 weight 升序，不用日期
template = "article_list.html"
page_template = "project.html"
paginate_by = 5
+++
```

### 3.2 加一个项目

一个项目 = 一个目录，`index.md` + 配图（同目录，用相对路径引用）：

```
content/projects/example-one/
├── index.md          # 中文
├── index.en.md       # 英文（可选）
└── cover.png         # 封面/截图
```

```toml
+++
title = "示例项目一"
date = 2026-09-11
weight = 1                       # 越小越靠前
description = "一句话介绍这个项目。"
[extra]
banner = "cover.png"             # 列表页卡片的缩略图（Zola 会裁成 480×240 的 webp）
hide_banner = true               # 详情页顶部不放大图（想要就删掉这行）
+++
```

正文直接写 Markdown，图片用同目录文件名：

```markdown
<img src="cover.png" alt="cover" style="max-width: 100%;" />
```

### 3.3 踩过的坑

| 坑 | 说明 |
| --- | --- |
| **封面不能用 SVG** | `banner = "cover.svg"` 会让 `resize_image` 报错（Zola 不支持缩放 SVG）。必须用 PNG/JPG/WebP。示例里是我用脚本生成的纯色 PNG 占位图，直接替换即可 |
| **`taxonomies` 会报错** | 在项目的 front matter 里写 `[taxonomies] tags = [...]` 时报 `taxonomy tags which is not defined in config.toml`（因为默认语言是 `zh-Hans`，而 `taxonomies` 只配在根级别）。示例里已经去掉了，想要标签得先把 taxonomies 配到语言块里 |
| **`lang` 不是 section 字段** | 我想把 nanolog 的按钮文案改成中文，试过在 `_index.md` 里写 `lang = "zh-Hans"`，Zola 直接报 `unknown field 'lang'`。所以浮层里的按钮文案**只能是英文或西语**（主题模板里写死了 `{% if lang == 'es' %}...{% else %}New Nanolog{% endif %}`）。真要中文就得覆盖 `templates/nanolog_list.html` |
| **占位链接会被 `zola check` 拦下** | 示例项目里的仓库地址我改成了 HTML 注释，否则 `zola check` 会因为 404 报 `Found broken external link(s)` |

---

## 四、验证结果

```
$ zola build
Building site...
-> Creating 6 pages (0 orphan) and 7 sections
Done in 2.1s.

$ zola check
-> Site content: 6 pages (0 orphan), 7 sections
Done in 2.8s.
```

| 检查项 | 结果 |
| --- | --- |
| `/nanolog/`、`/en/nanolog/`、`/projects/`、`/en/projects/` | 全部生成 ✅ |
| 随手记 2 条 + 单条页时间轴（`nanolog-higher`/`nanolog-lower`） | ✅ |
| 项目 2 个（列表卡片 2 张、封面被处理成 `processed_images/cover.*.webp`） | ✅ |
| 侧边栏三个入口 href | `/blog/`、`/nanolog/`、`/projects/` ✅ |
| `nanolog/style.css`、`nanolog/script.js` 被正确引用（带 cachebust） | ✅ |
| 发布浮层：按钮可见、点击后 `#nanolog-modal` 从 `none` 变 `flex`、页面无 JS 报错 | ✅（headless Chromium + CDP 实测） |
| 「Edit Post」链接 | 指向 `https://github.com/miliu2cc/blog/edit/main/content/nanolog/<文件>` ✅ |

---

## 五、文件清单

```
content/nanolog/_index.md / _index.en.md          # 栏目配置
content/nanolog/2026-09-08T21:14:00Z.md           # 示例条目
content/nanolog/2026-09-11T17:40:00Z.md           # 示例条目
content/projects/_index.md / _index.en.md         # 栏目配置
content/projects/example-one/{index.md,index.en.md,cover.png}
content/projects/example-two/{index.md,index.en.md,cover.png}
static/nanolog/style.css                          # 浮层样式（主题缺的部分）
static/nanolog/script.js                          # 发布脚本（加了判空）
zola.toml                                         # 侧边栏三个入口 + source_url
```
