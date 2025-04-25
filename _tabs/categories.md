---
title: Categories
icon: fas fa-folder
order: 2
layout: page
permalink: /categories/
---

{% assign categories = site.categories | sort %}
<ul>
  {% for category in categories %}
  <li>
    <a href="{{ '/categories/' | append: category[0] | slugify | prepend: '/' | append: '/' }}">
      {{ category[0] }} ({{ category[1].size }})
    </a>
  </li>
  {% endfor %}
</ul>
