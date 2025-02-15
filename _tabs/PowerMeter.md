---
layout: page
title: "PowerMeter"
icon: fas fa-bolt
order: 1
---

{% assign power_posts = site.posts | where_exp: "post", "post.categories contains 'PowerMeter'" %}

{% if power_posts.size > 0 %}
  {% include post-list.html posts=power_posts %}
{% else %}
  <p class="text-center">*No posts yet!*</p>
{% endif %}
