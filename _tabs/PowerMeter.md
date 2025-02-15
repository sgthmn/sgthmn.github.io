---
layout: page
title: "PowerMeter"
icon: fas fa-bolt
order: 1
---

{% assign power_posts = site.posts | where_exp: "post", "post.categories contains 'PowerMeter'" %}

{% if power_posts.size > 0 %}
  {% for post in power_posts %}
    <article class="card">
      <div class="card-body">
        <h2 class="card-title">
          <a href="{{ post.url }}">{{ post.title }}</a>
        </h2>
        <p class="post-meta text-muted">
          <i class="far fa-calendar-alt"></i> {{ post.date | date: "%B %d, %Y" }}
        </p>
        <p>{{ post.excerpt }}</p>
      </div>
    </article>
  {% endfor %}
{% else %}
  <p class="text-center">*No posts yet!*</p>
{% endif %}
