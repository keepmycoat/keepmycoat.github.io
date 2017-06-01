---
layout: default
title: CNN is Fake News
---

{% assign post = site.posts.first %}

<section class="post">
  <iframe width='1000' height='1024' frameborder='0' scrolling='no' src="https://docs.google.com/spreadsheets/d/15kkGAXHFeX1gn9EbzpHIXLwAaLCqdw_tWNTgeGPgT-8/pubhtml?gid=0&single=true&widget=true&headers=false"></iframe>
  {{ post.content | markdownify }}
</section>

{% include pagination.html %}
