---
title: Blog
icon: fas fa-pen-nib
order: 2
layout: page
---

{% for post in site.posts %}
<article>
  <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
  <p>{{ post.excerpt }}</p>
  <p><small><em>Posted on {{ post.date | date: "%B %d, %Y" }}</em></small></p>
  <hr>
</article>
{% endfor %}
