---
title: "오늘 배운 것을 정리하는 습관을 가지자! 2025.06~"
layout: archive
permalink: categories/til-2025
author_profile: true
sidebar_main: true
---

<!-- 공백이 포함되어 있는 카테고리 이름의 경우 site.categories['a b c'] 이런식으로! -->

***

{% assign posts = site.categories.til-2025 %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}