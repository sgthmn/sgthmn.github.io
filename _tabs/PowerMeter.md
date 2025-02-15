---
layout: page
title: "PowerMeter"
icon: fas fa-bolt
order: 1
---

<h2>Debugging Posts</h2>
<ul>
{% for post in site.posts %}
  <li>{{ post.title }} - Categories: {{ post.categories }}</li>
{% endfor %}
</ul>
