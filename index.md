---
layout: default
title: Abs0lut3Pwn4g3
description: Writeups
---
<h1>Latest Writeups</h1>

<ul>
  {% for post in site.posts %}
    <li>
      <h2><a href="writeups{{post.url}}">{{ post.title }}</a></h2>
      <p>{{ post.excerpt }}</p>
    </li>
  {% endfor %}
</ul>
