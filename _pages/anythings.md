---
layout: page
title: ANYthings
subtitle: Consulting in research and data science
use-site-title: true
permalink: /anythings/
description: ANYthings — my consulting practice in research and data science.
nav: true
nav_order: 4
display_categories: [ANYthings]
horizontal: false
---

<div class="anythings-intro">
  <p>
    <strong>ANYthings</strong> is my personal company, offering consulting on research
    and data science projects — from study design and data management to analysis,
    visualisation, and reporting.
  </p>
  <p>
    Past and ongoing engagements span indoor-climate science, microbiome work, R
    package development for sensor data, and methods consulting for clinical and
    epidemiological studies. A selection of these is shown below.
  </p>
  <p>
    If you have a question or a project where I might be of help, get in touch at
    <a href="mailto:anders@anythings.no">anders@anythings.no</a>.
  </p>
</div>

<!-- pages/anythings.md -->
<div class="projects">
{% assign categorized_projects = site.projects | where: "category", "ANYthings" %}
{% assign sorted_projects = categorized_projects | sort: "importance" %}
<div class="grid">
  {% for project in sorted_projects %}
    {% include projects.liquid %}
  {% endfor %}
</div>
</div>
