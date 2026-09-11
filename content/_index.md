+++
title = "我的博客"
sort_by = "date"
[extra]
# 不渲染页面大标题，hero 自己画
no_header = true

# 下面三组给 online 组件用（组件定义在仓库根目录的 templates/components.html）
# ⚠️ url / 邮箱 / 用户名都请换成你自己的
socials = [
    { name = "GitHub", url = "https://github.com/miliu2cc", icon = "github-logo" },
]
forges = [
    { name = "GitHub", url = "https://github.com/miliu2cc", icon = "github-logo" },
]
contacts = [
    { name = "邮箱", url = "mailto:you@example.com", icon = "envelope-simple" },
    { name = "Discord", url = "https://discord.com/users/你的ID", icon = "discord-logo" },
]
+++

<section class="snap-section hero-wrapper">
<div class="hero-container">
<div class="hero-content">
{{<retro_text text="我的博客" tag="h1"/>}}
<p style="font-size: 1rem; margin-bottom: 2rem; line-height: 1.6;">
这里是 <mark>我的博客</mark>，记录一些东西。<br>
把这句话换成你的自我介绍。
</p>
</div>

{{<retro_avatar src="/images/avatar.jpg" alt="头像"/>}}
</div>
<div id="scroll-target" style="position: absolute; bottom: 110px; left: 50%; width: 1px; height: 1px; z-index: -1;"></div>

<a class="scroll-indicator" href="#about" style="position: absolute; bottom: 120px; left: 50%; margin-left: -120px;">
<div style="margin-bottom: 28px; min-width: max-content;">
<div style="transform: rotate(5deg); display: inline-block; text-align: center;">
{{<arrow_note text="往下看<br>还有内容" target="scroll-target" start_dir="left" end_dir="top" color="accent" font_size="1rem" amplitude="30" spacing="10" stroke_width="2"/>}}
</div>
</div>
</a>
</section>

<section class="snap-section content-wrapper">
<div style="width: 100%;">

## 关于

<ul class="masonry">
<li>
<article>

**👋 你好，我是这个博客的主人。**

这里是我的自留地，写点技术笔记、折腾记录和随想。

TODO: 换成你自己的介绍。

</article>

<article>

{{<github_grid username="miliu2cc" color="#a78bfa" />}}

我一直挺喜欢写代码和解决问题的，上面的格子是最近一年的 GitHub 提交记录。

</article>
</li>
</ul>

## 联系我

<ul class="masonry">
<li>
<article class="online fancy-list">

<strong id="forges" class="title">Code</strong>

{{<online type="forges" section={section}/>}}

</article>

<article class="online fancy-list">

<strong id="contacts" class="title">Contacts</strong>

欢迎通过下面任意方式找我。

{{<online type="contacts" section={section}/>}}

</article>
</li>
</ul>

</div>
</section>
