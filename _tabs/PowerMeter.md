---
layout: tab
title: "PowerMeter"
icon: fas fa-stream
order: 1
---

## 🚴‍♂️ PowerMeter Project Posts

{% for post in site.posts %}
{% if post.categories contains "PowerMeter" %}
- **[{{ post.title }}]({{ post.url }})** - *{{ post.date | date: "%B %d, %Y" }}*
{% endif %}
{% endfor %}
