---
title: "study"
permalink: /study/
layout: archive
author_profile: true
---
공부한 걸 정리합니다📝
{% assign posts = site.categories.study %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
