+++
title = "My Blog"
sort_by = "date"
[extra]
no_header = true

socials = [
    { name = "GitHub", url = "https://github.com/miliu2cc", icon = "github-logo" },
]
forges = [
    { name = "GitHub", url = "https://github.com/miliu2cc", icon = "github-logo" },
]
contacts = [
    { name = "Email", url = "mailto:you@example.com", icon = "envelope-simple" },
    { name = "Discord", url = "https://discord.com/users/your-id", icon = "discord-logo" },
]
+++

<section class="snap-section hero-wrapper">
<div class="hero-container">
<div class="hero-content">
{{<retro_text text="My Blog" tag="h1"/>}}
<p style="font-size: 1rem; margin-bottom: 2rem; line-height: 1.6;">
Welcome to <mark>my blog</mark> — notes, tinkering logs and random thoughts.<br>
Replace this line with your own introduction.
</p>
</div>

{{<retro_avatar src="/images/avatar.jpg" alt="Avatar"/>}}
</div>
<div id="scroll-target" style="position: absolute; bottom: 110px; left: 50%; width: 1px; height: 1px; z-index: -1;"></div>

<a class="scroll-indicator" href="#about" style="position: absolute; bottom: 120px; left: 50%; margin-left: -120px;">
<div style="margin-bottom: 28px; min-width: max-content;">
<div style="transform: rotate(5deg); display: inline-block; text-align: center;">
{{<arrow_note text="Keep<br>scrolling" target="scroll-target" start_dir="left" end_dir="top" color="accent" font_size="1rem" amplitude="30" spacing="10" stroke_width="2"/>}}
</div>
</div>
</a>
</section>

<section class="snap-section content-wrapper">
<div style="width: 100%;">

## About

<ul class="masonry">
<li>
<article>

**👋 Hi, I'm the person behind this blog.**

This is my own corner of the web for tech notes, tinkering logs and random thoughts.

TODO: write your own introduction.

</article>

<article>

{{<github_grid username="miliu2cc" color="#a78bfa" />}}

I enjoy writing code and solving problems — the grid above is my GitHub activity over the past year.

</article>
</li>
</ul>

## Online

<ul class="masonry">
<li>
<article class="online fancy-list">

<strong id="forges" class="title">Code</strong>

{{<online type="forges" section={section}/>}}

</article>

<article class="online fancy-list">

<strong id="contacts" class="title">Contacts</strong>

Feel free to reach me through any of these.

{{<online type="contacts" section={section}/>}}

</article>
</li>
</ul>

</div>
</section>
