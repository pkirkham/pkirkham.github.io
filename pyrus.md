---
layout: archive
title: Pyrus Suite Of Oil And Gas Tools
permalink: /pyrus/
image:
  feature: feature-pyrus-1024x256.png
---

Pyrus is my attempt to package a set of oil and gas technical and commercial tools that I have developed and used over several decades into a more user-friendly piece of software. There are a variety of tools that cover a range of difference disciplines, including several for which free software alternatives are hard to find or don't exist. The software is modular in nature, and only a few of the tools that I have developed are currently implemented in Pyrus. It is my ambition to continue to release new modules and functionality into the future. Whilst this software is not open-source, it is made available free of charge as a courtesy to other engineers who may find it useful.

Incidentally, the name Pyrus is the [biological genus for the pear tree](https://en.wikipedia.org/wiki/Pear). So what? Well the initial incarnation of this suite of software tools was referred to as somewhat more cumbersome "Petroleum Economics And Resources" which has the acronym P.E.A.R. Rather than calling the software "Pear", the biological genus "Pyrus" sounds more sophisticated and just seemed like a better option.

<div class="button-container">
  <a href="{{ site.url }}/pyrus-download/" class="nav-button">
    <img src="{{ site.url }}/img/nav-download.png" alt="Download Pyrus">
    <div>Download Pyrus</div>
  </a>
  <a href="{{ site.url }}/pyrus-features/" class="nav-button">
    <img src="{{ site.url }}/img/nav-features.png" alt="Pyrus Features">
    <div>Pyrus Features</div>
  </a>
  <a href="{{ site.url }}/pyrus-about/" class="nav-button">
    <img src="{{ site.url }}/img/nav-about.png" alt="About Pyrus">
    <div>About Pyrus</div>
  </a>
</div>

## Documentation

Articles related to the development of the software are collected here.

<div class="tiles">
{% for post in site.categories.pyrus %}
  {% include post-grid.html %}
{% endfor %}
</div><!-- /.tiles -->