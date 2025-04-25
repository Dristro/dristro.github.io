---
title: Categories
icon: fas fa-folder
order: 4
layout: page
permalink: /categories/
---

{% assign categories = site.categories | sort %}
<ul>
  {% for category in categories %}
    <li>
      <a href="{{ '/categories/' | append: category[0] | append: '/' | relative_url }}">
        {{ category[0] }} ({{ category[1].size }})
      </a>
    </li>
  {% endfor %}
</ul>
