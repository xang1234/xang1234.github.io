---
layout: archive
permalink: /machine-learning/
title: "Machine Learning Posts"
author_profile: true
header:
  image: "/images/grenoble.jpeg"
---

<div>

  <ul class="post-list">

    {% capture tag %}{{ page.title | slugify }}{% endcapture %}

    {% for post in site.posts %}
      {% if post.tags contains "Visualization" %}

        <p padding:0px; margin:0px; class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</p>
        <h2 padding:0px; margin:0px;><a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></h2>
        {{ post.excerpt }}
        <a href="{{ post.url | prepend: site.baseurl }}" rel="nofollow">Continue reading &rarr;</a>

      {% endif %}
    {% endfor %}

  </ul>

</div>
