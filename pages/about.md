---
layout: page
title: About Sawtone
description: 不负
keywords: Xin Wang, Sawtone
comments: true
menu: 关于
permalink: /about/
---

I'm Sawtone.

一个目前 ENFJ 的平凡大学生，累并快乐着。

如果此博客对你有帮助，还请为此项目点亮star，感激不尽。

项目地址：https://github.com/Scoodtone/Scoodtone.github.io

## 联系

<ul>
{% for website in site.data.social %}
<li>{{website.sitename }}：<a href="{{ website.url }}" target="_blank">@{{ website.name }}</a></li>
{% endfor %}
</ul>


## Skill Keywords

{% for skill in site.data.skills %}
### {{ skill.name }}
<div class="btn-inline">
{% for keyword in skill.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
