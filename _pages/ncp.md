---
title: "NCP"
permalink: /ncp/
layout: archive
author_profile: true
---
NCP 포스팅 모음입니다.

{% assign posts = site.categories.ncp %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}