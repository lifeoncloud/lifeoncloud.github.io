---
title: "Featured"
permalink: /featured/
layout: archive
author_profile: true
---
좋아하는 포스팅 모음입니다.

{% assign posts = site.categories.featured %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}