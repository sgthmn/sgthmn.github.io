---
layout: PowerMeter
icon: fas fa-stream
order: 1
---
{% for post in site.posts %}
{% if post.categories contains "PowerMeter" %}
- **[{{ post.title }}]({{ post.url }})** - *{{ post.date | date: "%B %d, %Y" }}*
{% endif %}
{% endfor %}