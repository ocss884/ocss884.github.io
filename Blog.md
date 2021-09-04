---
layout: page
title: Blog
---
{% assign posts_by_year = site.texts | group_by_exp:"post", "post.date | date: '%Y'" %}

{% for year in posts_by_year reversed%}
<h2 style="text-align:center;">{{ year.name }}</h2>
<ul>
  {% for post in year.items reversed%}
    <li>
      {{post.date | date: "%Y-%m-%d"}} <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>

{% endfor %}