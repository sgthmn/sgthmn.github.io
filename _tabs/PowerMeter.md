---
layout: tab
title: "PowerMeter"
icon: fas fa-bolt  # Change to another FontAwesome icon if you want
order: 3
---

## 🚴‍♂️ PowerMeter Project Posts

{% assign power_posts = site.posts | where_exp: "post", "post.categories contains 'PowerMeter'" %}

{% if power_posts.size > 0 %}
  {% for post in power_posts %}
  - **[{{ post.title }}]({{ post.url }})** - *{{ post.date | date: "%B %d, %Y" }}*
  {% endfor %}
{% else %}
  *No posts yet!*
{% endif %}