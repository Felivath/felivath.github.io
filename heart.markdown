---
layout: default
title: Heart Color
permalink: /heart/
---

## Heart Color Controller

This page reads and updates the LED heart’s color via your Cloudflare Worker.

<div class="box">
  <h2>Current Status</h2>
  <p>
    <strong>Endpoint:</strong> <code id="endpoint">—</code><br/>
    <strong>Color:</strong> <code id="currentHex">—</code> ·
    <strong>Brightness:</strong> <span id="currentBri">—</span>
  </p>
  <div id="swatch" style="width:100%;height:80px;border-radius:12px;border:1px solid #692683; background:#ccc;"></div>
</div>

<div class="box" style="margin-top:1rem;">
  <h2>Pick a New Color</h2>

  <div style="display:flex; gap:1rem; align-items:center; flex-wrap:wrap;">
    <label>Color:
      <input type="color" id="picker" value="#ff33aa" style="vertical-align:middle;">
    </label>

    <label>Brightness:
      <input type="range" id="briRange" min="0" max="255" value="80" style="vertical-align:middle;">
      <input type="number" id="briNum" min="0" max="255" value="80" style="width:5.5rem; vertical-align:middle;">
    </label>

    <button id="saveBtn">Save</button>
  </div>

  <p id="msg" style="margin-top:.75rem;color:#444;"></p>
</div>

<noscript>
  <p><strong>Note:</strong> This page needs JavaScript enabled to talk to the API.</p>
</noscript>

<script>
(() => {
  // === SET THIS TO YOUR WORKER URL ===
  const API_URL = "https://heart-api.felivath3.workers.dev/";

  // Elements
  const $ = (id) => document.getElementById(id);
  const endpoint = $("endpoint");
  const swatch   = $("swatch");
  const currentHex = $("currentHex");
  const currentBri = $("currentBri");
  const picker   = $("picker");
  const briRange = $("briRange");
  const briNum   = $("briNum");
  const saveBtn  = $("saveBtn");
  const msg      = $("msg");

  endpoint.textContent = API_URL;

  function clamp(n, lo, hi) { n = Number(n); return Number.isFinite(n) ? Math.max(lo, Math.min(hi, n)) : lo; }
  function setSwatch(hex) { swatch.style.background = hex; }
  function normHex(h) {
    if (!h) return null;
    h = h.trim();
    if (h[0] !== "#") h = "#" + h;
    return /^#([0-9a-fA-F]{6}|[0-9a-fA-F]{8})$/.test(h) ? h.toUpperCase() : null;
  }
  function say(text, ok=true) {
    msg.textContent = text;
    msg.style.color = ok ? "#0a7a1b" : "#b00020";
  }

  // Keep range and number inputs in sync
  function syncBri(fromRange) {
    if (fromRange) briNum.value = briRange.value;
    else briRange.value = clamp(briNum.value, 0, 255);
  }
  briRange.addEventListener("input", () => syncBri(true));
  briNum.addEventListener("input", () => syncBri(false));

  async function loadStatus() {
    say("Loading…", true);
    try {
      const r = await fetch(API_URL, { cache: "no-store" });
      if (!r.ok) throw new Error("GET " + r.status);
      const j = await r.json();
      const hex = normHex(j.hex) || "#FF33AA";
      const b   = clamp(j.brightness ?? 80, 0, 255);
      currentHex.textContent = hex;
      currentBri.textContent = b;
      picker.value = hex;
      briRange.value = b;
      briNum.value = b;
      setSwatch(hex);
      say("Loaded.");
    } catch (e) {
      say("Could not load from API. Check API_URL or CORS.", false);
    }
  }

  async function save() {
    const hex = normHex(picker.value);
    const bri = clamp(briNum.value, 0, 255);
    if (!hex) { say("Please choose a valid hex like #RRGGBB.", false); return; }

    saveBtn.disabled = true;
    say("Saving…", true);
    try {
      const r = await fetch(API_URL, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ hex, brightness: bri, mode: "solid" })
      });
      if (!r.ok) throw new Error("POST " + r.status);
      // Update UI
      currentHex.textContent = hex;
      currentBri.textContent = bri;
      setSwatch(hex);
      say("Saved! The heart will update on its next poll.");
    } catch (e) {
      say("Failed to save. Check the Worker is deployed and CORS allows this origin.", false);
    } finally {
      saveBtn.disabled = false;
    }
  }

  saveBtn.addEventListener("click", save);
  loadStatus();
})();
</script>
