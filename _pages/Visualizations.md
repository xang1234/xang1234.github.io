---
layout: archive
permalink: /visualizations/
title: "Visualizations by tag"
author_profile: true
header:
  image: "/images/island.jpeg"
---


{%for post in site.posts %}
      {% if post.tags contains 'Visualization' %}
         {% <a href="#">post.title</a> %}
      {% endif %}
{% endfor %}
