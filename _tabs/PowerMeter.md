---
layout: home
title: "PowerMeter"
icon: fas fa-bolt
order: 1
pagination: false
---

{% assign power_posts = site.posts | where_exp: "post", "post.categories contains 'PowerMeter'" %}

{% for post in power_posts %}
  <div class="post-preview">
    <h1 class="post-title">
      <a href="{{ post.url }}">{{ post.title }}</a>
    </h1>
    <div class="post-meta">
      <span>
        <i class="far fa-calendar-alt"></i> {{ post.date | date: "%B %d, %Y" }}
      </span>
    </div>
    <p class="post-excerpt">{{ post.excerpt }}</p>
  </div>
  <hr>
{% endfor %}
