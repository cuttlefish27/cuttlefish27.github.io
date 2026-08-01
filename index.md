---
layout: default
title: Home
---

# Welcome

This blog is a collection of my interests and projects.

# Latest Post

{% assign latest_post = site.posts.first %}

<a href="{{ latest_post.url | relative_url }}">
  {{ latest_post.title }}
</a>

{{ latest_post.excerpt }}
