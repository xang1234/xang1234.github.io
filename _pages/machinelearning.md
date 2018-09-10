---
layout: archive
permalink: /machine-learning/
title: "Machine Learning Posts"
author_profile: true
classes: wide
header:
  image: "/images/grenoble.jpeg"
---

<div>

  <ul class="post-list">

    {% capture tag %}{{ page.title | slugify }}{% endcapture %}

    {% for post in site.posts %}
      {% if post.tags contains "Machine Learning"  %}
        {% include archive-single-mod.html  %}

      {% endif %}
    {% endfor %}

  </ul>

</div>

{% include paginator.html %}
