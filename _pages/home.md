---
layout: splash
permalink: /
hidden: true
classes: home-page
header:
  overlay_image: /assets/images/AdobeStock_184518220_lores.jpeg
  overlay_filter: "linear-gradient(100deg, rgba(8, 31, 60, 0.94) 0%, rgba(14, 64, 110, 0.82) 50%, rgba(23, 107, 175, 0.35) 100%)"
  eyebrow: "UC San Diego"
  actions:
    - label: "Explore our research"
      url: "/projects/"
      class: "btn--hero-primary"
    - label: "Join the lab"
      url: "/join/"
      class: "btn--hero-secondary"
excerpt: "We combine AI, physics and observations to understand and predict our changing climate, and train the next generation of climate and data scientists."
tagline: "**Understanding and predicting our changing climate.** We combine AI, physics and observations to study how aerosols, clouds and the wider climate system respond to human influence, and to build better tools for predicting it, from interpretable equations discovered in observations to differentiable models and rigorous benchmarks. Through our research and teaching, we train the next generation of climate and data scientists."

feature_row:
  - image_path: "/assets/images/MOSAIC_logo_transparent.png"
    alt: "MOSAIC logo with stylized ocean waves, clouds, and code braces"
    title: "MOSAIC"
    excerpt: "Discovering interpretable climate-model equations from observations with symbolic AI."
    url: "/projects/mosaic/"
    btn_class: "btn--primary"
    btn_label: "Learn more"
  - image_path: "/assets/images/JCM_logo.png"
    alt: "JAX-GCM differentiable climate model"
    title: "JAX-GCM"
    excerpt: "A fully differentiable climate model for hybrid ML and gradient-based optimization."
    url: "/projects/jaxgcm/"
    btn_class: "btn--primary"
    btn_label: "Learn more"
  - image_path: /assets/images/GAIA_logo.png
    alt: "GAIA Initiative"
    title: "GAIA Initiative"
    excerpt: "Harnessing AI to understand, forecast and steward Earth's complex systems."
    url: "/projects/gaia/"
    btn_class: "btn--primary"
    btn_label: "Learn more"
  - image_path: /assets/images/climatebench2_logo.png
    alt: "ClimateBench2 logo: a globe beside rising bars and a trend line with the numeral 2"
    title: "ClimateBench2"
    excerpt: "A probabilistic benchmark for physics-based, data-driven and hybrid climate models."
    url: "/projects/climatebench2/"
    btn_class: "btn--primary"
    btn_label: "Learn more"

# feature_row2:
#   - image_path: /assets/images/shiptracks_small.jpg
#     alt: "Shiptracks"
#     title: "Detecting ship tracks"
#     excerpt: "Using machine learning to automatically detect the brightening effect that shipping can have on clouds."
#     url: "/projects/shiptracks"
#     btn_class: "btn--primary"
#     btn_label: "Learn more"
#   - image_path: /assets/images/emulator_schematic.svg
#     alt: "Emulator schematic"
#     title: "Model Emulation for Calibration"
#     excerpt: "Developing climate model emulators for better parameter estimation and calibration."
#     url: "/projects/emulation/"
#     btn_class: "btn--primary"
#     btn_label: "Learn more"      
---

<div class="home-hiring">
  <div class="home-hiring__text">
    <span class="home-hiring__badge">We're hiring!</span>
    <p>Applications are open for Postdoctoral Researchers in AI for Climate Science on the <a href="{{ '/projects/mosaic/' | relative_url }}">MOSAIC</a> project, using agentic AI to discover better climate-model parameterizations.</p>
  </div>
  <a class="btn btn--large home-hiring__btn" href="{{ '/join/mosaic-postdoc/' | relative_url }}">Position details &amp; how to apply</a>
</div>

<p class="home-join"><strong>Interested in joining the lab?</strong> Current opportunities for postdocs, prospective PhD students and current UC San Diego students, and how to apply, are on our <a href="{{ '/join/' | relative_url }}">Join the Lab</a> page.</p>

<div class="home-section-header">
  <div>
    <p class="home-eyebrow">Research</p>
    <h2 id="project-highlights">Project Highlights</h2>
  </div>
  <a class="home-section-link" href="{{ '/projects/' | relative_url }}">All projects</a>
</div>

{% include feature_row class="home-projects" %}

{% include feature_row id="feature_row2" %}

<div class="home-section-header">
  <div>
    <p class="home-eyebrow">Latest</p>
    <h2 id="news">News</h2>
  </div>
</div>

{% assign sorted = site.news | sort: 'date' | reverse %}

<div id='short_news' style="display: block;">
  <ul class="home-news">
  {% for news in sorted limit:5 %}
    <li><time datetime="{{ news.date | date: '%Y-%m' }}">{{ news.date | date: "%b %Y" }}</time><div>{{ news.content | markdownify | remove: "<p>" | remove: "</p>" }}</div></li>
  {% endfor %}
  </ul>
  <a href="#" onclick="hideBlock('short_news'); showBlock('long_news'); return false;" class="btn home-news__toggle">Show all news</a>
</div>

<div id='long_news' style="display: none;">
  <ul class="home-news">
  {% for news in sorted %}
    <li><time datetime="{{ news.date | date: '%Y-%m' }}">{{ news.date | date: "%b %Y" }}</time><div>{{ news.content | markdownify | remove: "<p>" | remove: "</p>" }}</div></li>
  {% endfor %}
  </ul>
  <a href="#" onclick="hideBlock('long_news'); showBlock('short_news'); return false;" class="btn home-news__toggle">Show less</a>
</div>
