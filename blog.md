---
layout: page
title: Blog
permalink: /blog/
---
<!-- This is the "liquid loop" -->
{% for post in site.posts %}
  <p><a href="{{ post.url }}">{{ post.title }}</a></p>
  <p>{{ post.date | date: "%B %d, %Y" }}</p>
{% endfor %}

<!-- This is for math mode -->
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
