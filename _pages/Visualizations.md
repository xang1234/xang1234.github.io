---
layout: archive
permalink: /visualizations/
title: "Visualization Posts"
author_profile: true
header:
  image: "/images/island.jpeg"
---


{% capture tag %}{{ page.title | slugify }}{% endcapture %}

{% for post in site.posts %}
  {% if post.tags contains "Visualization" %}

    <p class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</p>
    {% include archive-single.html %}

  {% endif %}
{% endfor %}
