---
layout: home
title: "PowerMeter"
icon: fas fa-bolt
order: 1
---

{% assign power_posts = site.posts | where_exp: "post", "post.categories contains 'PowerMeter'" %}

{% for post in power_posts %}
  {{ post }}
{% endfor %}
