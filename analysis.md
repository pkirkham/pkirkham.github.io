---
layout: archive
title: Upstream Notebook
permalink: /analysis/
---

My job involves a wide variety of different skillsets from the technical disciplines such as geology, geophysics and reservoir engineering through to commercial and management disciplines such as economics, legal and projects. Keeping on top of all these different disciplines is difficult at the best of times, and thus I occasionally document certain aspects of work for future reference.

<div class="tiles">
{% for post in site.categories.analysis %}
  {% include post-grid.html %}
{% endfor %}
</div><!-- /.tiles -->