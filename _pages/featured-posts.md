---
title: "k8s"
permalink: /k8s/
layout: category
author_profile: true
taxonomy: featured-posts
---
{% assign posts = site.categories.featured-posts %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}