---
title: "GCP"
permalink: /gcp/
layout: archive
author_profile: true
---
GCP의 각 기능에 대한 설명과 팁을 담았습니다 🧐
{% assign posts = site.categories.gcp %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}