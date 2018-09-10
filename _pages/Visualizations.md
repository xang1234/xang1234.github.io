---
layout: grid
permalink: /visualizations/
title: "Visualization Posts"
author_profile: true
header:
  image: "/images/island.jpeg"
---


<div>

  <ul class="post-list">

    {% capture tag %}{{ page.title | slugify }}{% endcapture %}

    {% for post in site.posts %}
      {% if post.tags contains "Visualization" %}
        {% include archive-single-mod.html %}

      {% endif %}
    {% endfor %}

  </ul>

</div>
