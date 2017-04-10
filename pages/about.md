---
layout: page
title: About Me
description: 没人比我会更恨我自己
keywords: 毛运
comments: true
menu: 关于我
permalink: /about/
---

我是毛运




## Skill Keywords

#### Software Language Keywords
<div class="btn-inline">
    {% for keyword in site.skill_language_keywords %}
    <button class="btn btn-outline" type="button">{{ keyword }}</button>
    {% endfor %}
</div>

#### Web Developer Keywords
<div class="btn-inline">
    {% for keyword in site.skill_web_app_keywords %}
    <button class="btn btn-outline" type="button">{{ keyword }}</button>
    {% endfor %}
</div>
