---
layout: default
title: Supravat Mondal wrote blogs &#8739; Magento2, Open Source and more
description: Here you can explore everything I wrote on this website. It allows you to follow my journey through time
image: /assets/img/supravat-mondal-logo-picture.webp
permalink: /blogs.html
---
**[Home](/) >> Blog**

## Latest articles:

<p>Here you can explore everything I wrote 📝 on this website. It allows you to follow my journey through time. Get updates by subscribing to <a href="/sitemap.xml">RSS</a>.</p>


{% assign postsByYear = site.posts | group_by_exp:"post", "post.date | date: '%Y'" %}
{% for year in postsByYear %}
<h4>{{ year.name }}</h4>
{% assign postsByMonth = year.items | group_by_exp:"post", "post.date | date: '%B'" %}
<ul id="posts">
{% for month in postsByMonth %}


  {% for post in month.items %}
    <li style="display:flex">
      <date><time>{{ post.date | date: "%-d %b" }}</time></date>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}

{% endfor %}
</ul>
{% endfor %}