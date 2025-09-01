---
layout: default
title: Heart Color
permalink: /heart/
---

## Heart Color Controller

This page shows the current color the heart will fetch from this site, and helps you generate the JSON/text you can paste into **`color.json`** (or **`color.txt`**) in the repo.

<div class="box">
  <h2>Current Status</h2>
  <p>
    <strong>Loaded from:</strong> <code id="source-file">—</code><br/>
    <strong>Color:</strong> <code id="current-hex">—</code>,
    <strong>Brightness:</strong> <span id="current-bright">—</span><br/>
  </p>
  <div id="swatch" style="width:100%;height:80px;border-radius:12px;border:1px solid #692683; background:#ccc;"></div>
</div>

<div class="box" style="margin-top:1rem;">
  <h2>Pick a New Color</h2>
  <p>
    <label>Color: <input type="color" id="picker" value="#ff33aa"></label>
    <label style="margin-left:1rem;">Brightness (0–255): 
      <input type="number" id="bri" value="80" min="0" max="255" style="width:6rem;">
    </label>
  </p>
  <p>
    <button id="copy-json">Copy JSON for <code>color.json</code></button>
    <button id="copy-hex" style="margin-left:.5rem;">Copy Hex for <code>color.txt</code></button>
  </p>
  <pre id="preview" style="white-space:pre-wrap;border:1px solid #692683;border-radius:12px;padding:12px;"></pre>
  <p>
    After copying, open the file in GitHub and paste:<br/>
    • <a href="https://github.com/Felivath/felivath.github.io/edit/main/color.json">Edit <code>color.json</code></a><br/>
    • <a href="https://github.com/Felivath/felivath.github.io/edit/main/color.txt">Edit <code>color.txt</code></a>
  </p>
</div>

<script>
(async function(){
  // Elements
  const srcEl = document.getElementById('source-file');
  const hexEl = document.getElementById('current-hex');
  const briEl = document.getElementById('current-bright');
  const swatch = document.getElementById('swatch');
  const picker = document.getElementById('picker');
  const bri = document.getElementById('bri');
  const preview = document.getElementById('preview');
  const btnJson = document.getElementById('copy-json');
  const btnHex = document.getElementById('copy-hex');

  // Helpers
  const clamp = (n, lo, hi) => Math.max(lo, Math.min(hi, n|0));
  function updatePreview(){
    const hex = picker.value;
    const b = clamp(+bri.value || 80, 0, 255);
    const json = JSON.stringify({hex, brightness: b, mode: "solid"}, null, 2);
    preview.textContent = json + "\n\nFor color.txt, use hex only:\n" + hex;
  }

  function setSwatch(hex){
    swatch.style.background = hex;
  }

  // Try JSON first, then text
  let loaded = false;
  try {
    const r = await fetch('/color.json', {cache:'no-store'});
    if (r.ok) {
      const data = await r.json();
      const hex = (data.hex || "#000000").toUpperCase();
      const b = ('brightness' in data) ? data.brightness : '—';
      srcEl.textContent = '/color.json';
      hexEl.textContent = hex;
      briEl.textContent = b;
      setSwatch(hex);
      if (typeof b === 'number') bri.value = b;
      picker.value = hex;
      loaded = true;
    }
  } catch(e){}

  if (!loaded) {
    try {
      const r2 = await fetch('/color.txt', {cache:'no-store'});
      if (r2.ok) {
        let text = (await r2.text()).trim();
        if (text.startsWith('#')) text = text;
        else if (/^[0-9A-Fa-f]{6}$/.test(text)) text = '#'+text;
        srcEl.textContent = '/color.txt';
        hexEl.textContent = text.toUpperCase();
        briEl.textContent = '—';
        setSwatch(text);
        picker.value = text;
        loaded = true;
      }
    } catch(e){}
  }

  if (!loaded) {
    srcEl.textContent = '— (not found)';
    hexEl.textContent = '—';
    briEl.textContent = '—';
  }

  // Live preview & copy buttons
  updatePreview();
  picker.addEventListener('input', updatePreview);
  bri.addEventListener('input', updatePreview);

  btnJson.addEventListener('click', async () => {
    const json = preview.textContent.split("\n\nFor color.txt")[0];
    await navigator.clipboard.writeText(json);
    btnJson.textContent = 'Copied!';
    setTimeout(()=>btnJson.textContent='Copy JSON for color.json', 900);
  });

  btnHex.addEventListener('click', async () => {
    const hex = picker.value;
    await navigator.clipboard.writeText(hex);
    btnHex.textContent = 'Copied!';
    setTimeout(()=>btnHex.textContent='Copy Hex for color.txt', 900);
  });
})();
</script>
