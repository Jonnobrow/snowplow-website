---
layout: page
title: Authors
---
<ul style="list-style: none;">
  {% for author in site.authors %}
    <li>
      <h2><a href="{{ author.url }}">{{ author.name }}</a></h2>
      <h3>{{ author.short_description }}</h3>
      <p>{{ author.long_description }}</p>
      <hr />
      <a href="mailto:{{ author.email }}">{{author.email}}</a>
    </li>
  {% endfor %}
</ul>
