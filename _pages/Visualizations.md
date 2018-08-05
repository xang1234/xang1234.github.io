---
layout: archive
permalink: /visualizations/
title: "Visualizations by tag"
author_profile: true
header:
  image: "/images/island.jpeg"
---


{% capture tag %}{{ page.title | slugify }}{% endcapture %}

{% for post in site.posts %}
  {% if post.tags contains Visualization %}
  <li>
    <p class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</p>
    <h2><a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></h2>
    {{ post.excerpt }}
    <a href="{{ post.url | prepend: site.baseurl }}" rel="nofollow">Continue reading &rarr;</a>
  </li>
  {% endif %}
{% endfor %}
