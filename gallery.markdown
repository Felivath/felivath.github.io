---
layout: default
title: Gallery
permalink: /gallery/
---


<div class="gallery">
  {% assign thumbs = site.static_files | where_exp: "file", "file.path contains 'assets/gallery/thumbs'" %}
  {% for thumb in thumbs %}
    {% assign full = thumb.path | replace: '/thumbs', '/full' %}
    <div class="gallery-item">
      <img src="{{ thumb.path | relative_url }}" data-full="{{ full | relative_url }}" alt="Gallery image">
    </div>
  {% endfor %}
</div>

<!-- Lightbox Modal -->
<div id="lightbox" class="lightbox">
  <span class="close">&times;</span>
  <span class="prev">&#10094;</span>
  <img class="lightbox-content" id="lightbox-img">
  <span class="next">&#10095;</span>
</div>

