---
title: "k8s"
permalink: /k8s/
layout: archive
author_profile: true
---
쿠버네티스 포스팅 모음입니다.

{% assign posts = site.categories.k8s %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}