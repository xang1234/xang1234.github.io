---
layout: archive
permalink: /visualizations/
title: "Visualizations by tag"
author_profile: true
header:
  image: "/images/island.jpeg"
---

{% include base_path %}
{% include group-by-array collection=site.posts field="tags" %}

{% for post in site.posts %}
{% if post.tags contains "Visualization" %}
  {% include archive-single.html %}
{% endfor %}
