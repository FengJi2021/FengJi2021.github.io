---
layout: page
title: Fragments
description: fragments 索引页
keywords: fragments
comments: false
menu: 片段
permalink: /fragments/
---

> Collect fragments of knowledge, and then put them together to form a whole.

<a href="{{ site.url }}/fragments/">全部 <span class="octicon octicon-chevron-right"></span></a>

<ul class="listing">
{% for item in site.fragments %}
{% if item.title != "Fragment Template" %}
<li class="listing-item" tags="{% for tag in item.tags %}{{ tag }} {% endfor %}">
  <a href="{{ site.url }}{{ item.url }}">{{ item.title }}</a>
  {% for tag in item.tags %}
  <a style="font-size:12px;color:gray;font-style:italic;display:inline-block;margin:0 0 0 4px;padding:0 4px;background-color:lightgray;" href="{{ site.url }}/fragments/?tag={{ tag }}" title="{{ tag }}">{{ tag }}</a>
  {% endfor %}
</li>
{% endif %}
{% endfor %}
</ul>

<script>
jQuery(function() {
    function getUrlParam(name) {
        var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)");
        var r = window.location.search.substr(1).match(reg);
        if (r != null) return r[2]; return null;
    }

    var tag = getUrlParam('tag');
    if (tag == undefined || tag === '') {
        return;
    }

    $(".listing-item").each(function() {
        if ($(this).attr('tags').indexOf(tag) < 0) {
            $(this).css('display', 'none');
        }
    });

});
</script>
