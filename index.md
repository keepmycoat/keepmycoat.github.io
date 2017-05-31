---
layout: default
title: 
---

{% assign post = site.posts.first %}

<section class="post">
  <h1><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></h1>
  <p class="post-meta">{{ post.date | date: "%d/%m/%Y" }}</p>
  <iframe width='1000' height='1024' frameborder='0' scrolling='no' src="https://docs.google.com/spreadsheets/d/15kkGAXHFeX1gn9EbzpHIXLwAaLCqdw_tWNTgeGPgT-8/pubhtml?gid=0&single=true&widget=true&headers=false"></iframe>
  {{ post.content | markdownify }}
</section>

{% include pagination.html %}
