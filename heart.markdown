---
layout: default
title: Heart Color
permalink: /heart/
---

## Heart Color

Use the button below to open a form that updates the heart color.  
Once submitted, GitHub Actions will write `color.json` automatically.

<p>
  <a class="btn" href="https://github.com/Felivath/felivath.github.io/issues/new?template=heart_color.yml">
    Open Color Update Form
  </a>
</p>

<div class="box">
  <h2>Current Status</h2>
  <p>
    <strong>Source:</strong> <code id="source-file">—</code><br/>
    <strong>Color:</strong> <code id="current-hex">—</code>,
    <strong>Brightness:</strong> <span id="current-bright">—</span>
  </p>
  <div id="swatch" style="width:100%;height:80px;border-radius:12px;border:1px solid #692683; background:#ccc;"></div>
</div>

<script>
(async function(){
  const srcEl = document.getElementById('source-file');
  const hexEl = document.getElementById('current-hex');
  const briEl = document.getElementById('current-bright');
  const swatch = document.getElementById('swatch');

  function set(hex,bri){
    srcEl.textContent = '/color.json';
    hexEl.textContent = hex.toUpperCase();
    briEl.textContent = (bri ?? '—');
    swatch.style.background = hex;
  }

  try {
    const r = await fetch('/color.json', {cache:'no-store'});
    if (r.ok) {
      const j = await r.json();
      set(j.hex || '#000000', j.brightness);
      return;
    }
  } catch(e){}

  try {
    const r2 = await fetch('/color.txt', {cache:'no-store'});
    if (r2.ok) {
      let t = (await r2.text()).trim();
      if (!t.startsWith('#') && /^[0-9A-Fa-f]{6}$/.test(t)) t = '#'+t;
      srcEl.textContent = '/color.txt';
      hexEl.textContent = t.toUpperCase();
      briEl.textContent = '—';
      swatch.style.background = t;
      return;
    }
  } catch(e){}

  srcEl.textContent = '— (not found)';
})();
</script>
