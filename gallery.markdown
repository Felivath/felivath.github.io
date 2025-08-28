---
layout: default
title: Gallery
permalink: /gallery/
---


<div class="gallery">
  {% for image in site.static_files %}
    {% if image.path contains 'assets/gallery' %}
      <div class="gallery-item">
        <img src="{{ image.path | relative_url }}" alt="Gallery image">
      </div>
    {% endif %}
  {% endfor %}
</div>

<!-- Lightbox Modal -->
<div id="lightbox" class="lightbox">
  <span class="close">&times;</span>
  <span class="prev">&#10094;</span>
  <img class="lightbox-content" id="lightbox-img">
  <span class="next">&#10095;</span>
</div>

