# 首页组件说明（retro_text / retro_avatar / arrow_note / github_grid / games / online）

记录"从 Axenide 的站点复刻首页组件"的做法、主题跟上游的方式、以及还需要你填的内容。

> 随手记（nanolog）和项目（projects）两个栏目见 `SECTIONS.md`。

> 现状：**主题没有任何本地改动**，跟上游 `dev` 分支的 tip；`age` 组件已按你的要求移除。

---

## 一、主题怎么跟（可以跟 dev，而且应该跟）

| | |
| --- | --- |
| 上游仓库 | `codeberg.org/Axenide/Axetrine` |
| 你钉的 commit | `3714762` = **上游 `dev` 分支的 tip** |
| `dev` 与 `main` 的关系 | `dev` 只领先 `main` 一个提交：`3714762 fix: github_grid shows 0 contributions in Tera v2`（只改了 1 行） |
| 本地改动 | **无**（`git -C themes/axetrine status` 干净） |

那个提交修的是 Tera v2 的 bug：循环里 `set` 不跨迭代导致 GitHub 贡献数恒为 0（修前实测 `0 contributions`，修后 `53 contributions`）。**它没有合进 `main`**，所以：

- 跟随 `dev` → 白拿这个修复，零本地补丁；
- 只跟 `main`（`5659fa8`）→ 贡献图恒为 0，必须自己打这一行。

`.gitmodules` 里已经声明了跟随的分支：

```ini
[submodule "themes/axetrine"]
	path = themes/axetrine
	url = https://codeberg.org/Axenide/Axetrine.git
	branch = dev
```

跟新上游：

```bash
git submodule update --remote themes/axetrine   # 拉到 dev 最新
git add themes/axetrine && git commit -m "chore: 更新主题"
```

⚠️ 代价：`dev` 是开发分支，上游可能推未打磨的东西。更新后**务必本地 `zola build` 一次**再推。
🛈 Codeberg 有时连不上（本机实测 `git fetch` 出现过 504），失败重试即可，仓库里已有对象时不影响构建。

---

## 二、组件在哪、怎么调用

现在页面上用到 **4 个主题组件** + 1 个站点自定义组件（`online`）：

| 组件 | 来源 | 调用写法 | 位置 |
| --- | --- | --- | --- |
| `retro_text` | 主题 | `{{<retro_text text="我的博客" tag="h1"/>}}` | hero |
| `retro_avatar` | 主题 | `{{<retro_avatar src="/images/avatar.jpg" alt="头像"/>}}` | hero |
| `arrow_note` | 主题 | `{{<arrow_note text="往下看<br>还有内容" target="scroll-target" start_dir="left" end_dir="top" .../>}}` | hero 下方（指向页面里的 `#scroll-target`） |
| `github_grid` | 主题 | `{{<github_grid username="miliu2cc" color="#a78bfa"/>}}` | 关于 第 2 张卡 |
| `online` | **本站自定义**（`templates/components.html`） | `{{<online type="forges" section={section}/>}}` / `type="contacts"` | Code / Contacts 卡 |

样式都在主题里（`sass/components/_retro-*.scss`、`sass/components/_arrow-note.scss`、`sass/home/_*.scss`），已由 `themes/axetrine/sass/custom.scss` 编译进 `style.css`，**不用你动**。

### 已放弃：指向侧边栏的箭头

试过两版都放弃了，结论是**不用**：

1. **`arrow_note` 指向 `#site-sidebar` → 出界**。主题的方向名语义是「元素边界 **减** spacing」（`case 'left': x = r.left - spacing`），而侧边栏 `inset-inline-start: 0` 固定在 `x=0`，终点落在 `x = -12 ~ -18`，整条线画到屏幕外（Chromium + CDP 实测路径 `M 576 1348 C 576 1318, -42 450, -12 450`）。
2. **改成指向旁边的小锚点** → 箭头缩到 44px，但末端切向朝右下，看起来不像「指向左边」。
3. **手写静态 SVG 小箭头**（44×26，曲线末端与箭头顶点偏差 0.00px）→ 形态没问题，但最终决定也不需要。

所以两页的 `sidebar-hint`（文字 + SVG 箭头 + 内嵌 `<style>`）已全部删除，产物里 `sidebar-hint` / `sidebar-anchor` 均为 0 次。想重新加的话，直接在介绍卡片末尾放一个 `<div>` + 内联 SVG 即可（不要用 `arrow_note` 指侧边栏，会踩上面第 1 条的坑）。

### 关于 `online`：为什么它在站点仓库里

`online` 不是主题组件——它是作者在自己站点仓库里定义的（用于渲染 socials / forges / contacts 三组链接）。所以在仓库根目录建了 `templates/components.html` 放它，**实测这不会覆盖主题的其它 32 个组件**（两者共存），这正是 Tera v2 支持的局部覆盖方式。

它读数据的位置是 `section.extra`：

```toml
# content/_index.md 与 _index.en.md 的 [extra] 里
forges = [   # 渲染进 "Code" 卡片
    { name = "GitHub", url = "https://github.com/miliu2cc", icon = "github-logo" },
]
contacts = [ # 渲染进 "Contacts" 卡片
    { name = "邮箱", url = "mailto:you@example.com", icon = "envelope-simple" },
    { name = "Discord", url = "https://discord.com/users/你的ID", icon = "discord-logo" },
]
```

`icon` 用的是 [Phosphor](https://phosphoricons.com) 图标名，SVG 会从 `themes/axetrine/icons/phosphor/` 内联进页面（无需额外文件）。

---

## 三、已移除的组件

| 组件 | 状态 | 说明 |
| --- | --- | --- |
| `age` | **已移除** | 主题把作者生日写死，想改成自己的必须打主题补丁；你选择不改主题 → 不用。`zola.toml` 里的 `birth_date` 也已删除 |
| `badges`（友链徽章） | **已移除并清理** | 首页的 `## 友链徽章` 小节和 `{{<badges .../>}}` 已删；`static/badges/`（31 张图片）与两个首页的 `[extra].badges` 也一并删掉了。想恢复需要重新放回图片和数据 |
| `games`（Recently playing） | **已移除并清理** | 卡片和 `{{<games .../>}}` 已删，`zola.toml` 里的 `discord_user_id` 也删了；想恢复见第五节 |

> `age` 移除的技术原因记录：Tera v2 的 `date(format="%s")` 过滤器在**组件参数**里拿到日期字符串时**不解析**（会返回当前时间，实测 `{{<age now_value="2001-06-12"/>}}` 渲染出 0 岁），只有主题内部从 config 取值再转时间戳才有效——即必须改主题。


---

## 四、还需要你填的内容

| 位置 | 现状 | 说明 |
| --- | --- | --- |
| `content/_index.md` / `_index.en.md` → `forges` | `https://github.com/miliu2cc` | 换成你的仓库/主页 |
| 同上 → `contacts` | `you@example.com`、`https://discord.com/users/你的ID` | **占位符，必须换** |
| 同上 → `github_grid` 的 `username="miliu2cc"` | `miliu2cc` | 换成你的 GitHub 用户名；构建期会请求第三方接口 |
| `static/images/avatar.jpg` | 作者头像 | 换成你自己的 |

---

## 五、构建期外呼

| 组件 | 请求 | 状态 |
| --- | --- | --- |
| `github_grid` | `https://github-contributions-api.jogruber.de/v4/<username>` | **仍在用**，必需；失败则整站构建失败，重跑通常能过 |
| `retro_text` | 浏览器端加载 `fonts.googleapis.com` 的 Bytesized 字体 | 不影响构建 |
| ~~`games`~~ | ~~`https://discord.com/api/*`~~ | 卡片已移除，不再请求 |

### 想恢复 `games` 卡片的话

它需要构建时提供 `DISCORD_TOKEN`（一个能代表你身份的 Discord 令牌）+ 可选 `STEAMGRIDDB_API_KEY`，没有时降级显示 `(Games list unavailable - Discord Token missing or widget not found)`。恢复步骤：把卡片加回首页 → `zola.toml` 的 `[extra]` 里加回 `discord_user_id = "你的ID"` → 本地 `DISCORD_TOKEN=xxx zola build` → Cloudflare Pages 里把 token 加成环境变量（标 Secret）。

---

## 六、验证结果（本地 `zola build`，2.5s）

| 检查项 | 中文页 `/` | 英文页 `/en/` |
| --- | --- | --- |
| `retro-word` / `retro-avatar-container` / hero 箭头 | ✅ | ✅ |
| 侧边栏提示（`sidebar-hint` / `sidebar-anchor`） | 已删除，0 次 | 已删除，0 次 |
| `github_grid` | `53 contributions` + 370 格子 | 同 |
| Code 卡片（`id="forges"`） | GitHub 链接 + `github-logo` 图标（SVG 已内联） | 同 |
| Contacts 卡片（`id="contacts"`） | 邮箱 + Discord，图标已内联 | 同 |
| `badges-marquee` / `fancy-list games` | 各 0 次（已移除） | 各 0 次 |
| `age` | 0 次（已移除） | 0 次 |
| 小节标题 | 关于 / 联系我 | About / Online |
| 主题工作树 | 干净，无本地改动 | — |

---

## 七、文件清单

```
.gitmodules                                 # branch = dev（跟随上游 dev）
content/_index.md                           # 中文首页：hero + 关于 + Code/Contacts（含侧边栏箭头）
content/_index.en.md                        # 英文首页：同结构
templates/components.html                   # 站点的 online 组件（Code / Contacts 卡片用）
zola.toml                                   # 站点配置（accent_color / 日期格式 / 时区 / 语言块）
static/images/avatar.jpg                     # 占位头像，请替换（static/ 现在只剩这一个文件）
HOMEPAGE.md                                 # 本文件
.gitignore                                  # 忽略 public/
```
