---
title: "Kubernetes"
permalink: /k8s/
layout: archive
author_profile: true
---
쿠버네티스를 공부하며 정리한 글들입니다💙   
{% assign posts = site.categories.k8s %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}