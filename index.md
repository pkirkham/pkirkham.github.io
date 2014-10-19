---
layout: archive
permalink: /
title: 
---

<div class="page-lead" style="background-image:url(//pkirkham.github.io/images/background-platform-sunset-1600x800.jpg)">
  <div class="wrap page-lead-content">
	<h1>Peter Kirkham</h1>
	<h2>Upstream Without A Paddle Blog</h2>
	<a href="//pkirkham.github.io/blog/" class="btn-inverse">Blog</a> &nbsp; <a href="//pkirkham.github.io/analysis/" class="btn-inverse">Analysis Notes</a> &nbsp; <a href="//pkirkham.github.io/pyrus/" class="btn-inverse">Pyrus Suite</a>
  </div><!-- /.page-lead-content -->
</div><!-- /.page-lead -->

<div class="tiles">
{% for post in site.posts %}
	{% include post-list.html %}
{% endfor %}
</div><!-- /.tiles -->