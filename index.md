---
layout: default
title: Abs0lut3Pwn4g3
description: writeups
---
<h1>Latest Writeups</h1>

<ul>
  {% for post in site.posts %}
    <li>
      <h2><a href="http://abs0lut3pwn4g3.github.io/writeups{{post.url}}">{{ post.title }}</a></h2>
      <p>{{ post.excerpt }}</p>
    </li>
  {% endfor %}
</ul>
