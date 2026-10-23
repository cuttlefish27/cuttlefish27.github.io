---
layout: default
title: Home
---

# Welcome

Welcome to my blog documenting my interests and projects. Check out the projects page to see a full list of projects.
<br>
<br>
Below is my latest post, enjoy reading!

___________________________________________________________

<br>
# Latest Post

{% assign latest_post = site.posts.first %}

<a href="{{ latest_post.url | relative_url }}">
  {{ latest_post.title }}
</a>

{{ latest_post.excerpt }}

__________________________________________________________

<br>