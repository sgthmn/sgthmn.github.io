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

<style>
  .post-list { padding: 20px; }
  .post-card {
    border: 1px solid #ccc;
    padding: 15px;
    border-radius: 8px;
    background: #fff;
    margin-bottom: 20px;
    box-shadow: 2px 2px 10px rgba(0, 0, 0, 0.1);
  }
  .post-card h3 {
    margin-bottom: 5px;
  }
  .post-meta {
    font-size: 0.9em;
    color: #666;
  }
</style>