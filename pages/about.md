---
layout: page
title: About
description: Trace my interests and thoughts.
keywords: Feng Ji
comments: true
menu: About
permalink: /about/
---

This is a personal blog of Feng Ji
Trace my interests and sammerize solutions to problems I met.


## Skill Keywords

{% for skill in site.data.skills %}
### {{ skill.name }}
<div class="btn-inline">
{% for keyword in skill.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
