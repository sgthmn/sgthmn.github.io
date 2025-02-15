---
layout: page
title: "PowerMeter"
icon: fas fa-bolt
order: 1
---

<div class="post-list">
  <h2>🚴‍♂️ PowerMeter Project Posts</h2>

  {% assign power_posts = site.posts | where_exp: "post", "post.categories contains 'PowerMeter'" %}

  {% if power_posts.size > 0 %}
    {% for post in power_posts %}
      <div class="post-card">
        <h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
        <p class="post-meta">
          <i class="far fa-calendar-alt"></i> {{ post.date | date: "%B %d, %Y" }}
        </p>
        <p>{{ post.excerpt }}</p>
      </div>
    {% endfor %}
  {% else %}
    <p>*No posts yet!*</p>
  {% endif %}
</div>

