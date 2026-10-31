---
title: "Projects"
permalink: /projects/
classes: projects-page
excerpt: "Current and earlier research projects from the Climate Analytics Lab at UC San Diego."
---

{% assign projects = site.posts | where_exp: "p", "p.hidden != true" | sort: "order" %}
{% assign current = projects | where: "status", "current" %}
{% assign earlier = projects | where: "status", "earlier" %}

<header class="projects-intro">
  <p class="home-eyebrow">Research</p>
  <h1 id="page-title">Projects</h1>
  <p>Our current work builds AI-enabled tools for understanding and predicting the climate system. Earlier projects use machine learning and satellite data to study aerosol–cloud interactions and to emulate climate models.</p>
</header>

<div class="home-section-header">
  <div>
    <h2 id="current">Current projects</h2>
  </div>
</div>

<div class="project-grid project-grid--current">
{% for p in current %}{% include project_card.html project=p %}{% endfor %}
</div>

<div class="home-section-header">
  <div>
    <h2 id="earlier">Earlier work</h2>
  </div>
</div>

<div class="project-grid">
{% for p in earlier %}{% include project_card.html project=p %}{% endfor %}
</div>

{% assign all_tags = site.tags | sort %}
<div class="project-topics">
  <h2 id="topics">Browse by topic</h2>
  <ul>
  {% for tag in all_tags %}
    <li><a href="{{ tag[0] | slugify | prepend: '#' | prepend: site.tag_archive.path | relative_url }}">{{ tag[0] }} <span>{{ tag[1].size }}</span></a></li>
  {% endfor %}
  </ul>
</div>
