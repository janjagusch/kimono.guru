---
title: Kimono Market
layout: default
---

<h1>Kimonos for Sale</h1>

<!-- Filters -->
<form id="filters" style="display:grid;gap:12px;grid-template-columns:repeat(auto-fit,minmax(180px,1fr));margin:16px 0;">
  <label>
    <span>Size</span>
    <select id="filter-size">
      <option value="">Any</option>
      {% assign sizes = site.data.kimonos | map:'size' | uniq | sort %}
      {% for s in sizes %}<option value="{{ s | downcase }}">{{ s }}</option>{% endfor %}
    </select>
  </label>

  <label>
    <span>Color</span>
    <select id="filter-color">
      <option value="">Any</option>
      {% assign colors = site.data.kimonos | map:'color' | uniq | sort %}
      {% for c in colors %}<option value="{{ c | downcase }}">{{ c }}</option>{% endfor %}
    </select>
  </label>

  <label>
    <span>Status</span>
    <select id="filter-status">
      <option value="">Any</option>
      <option value="available">Available</option>
      <option value="sold">Sold</option>
    </select>
  </label>

  <input id="filter-q" type="search" placeholder="Search brand/model…" style="padding:8px;">
  <button type="button" id="reset-filters">Reset</button>
</form>

<!-- Grid of tiles -->
<div id="grid" style="display:grid;gap:16px;grid-template-columns:repeat(auto-fill,minmax(260px,1fr));">
{% for item in site.data.kimonos %}
  {% assign status = item.status | default: 'available' %}
  <article class="gi-card"
           data-size="{{ item.size | downcase }}"
           data-color="{{ item.color | downcase }}"
           data-status="{{ status | downcase }}"
           data-text="{{ item.brand | append:' ' | append:item.model | downcase }}"
           style="border:1px solid #e5e7eb;border-radius:14px;overflow:hidden;background:#fff;display:flex;flex-direction:column;">
    <div style="position:relative;">
      {% if item.image %}
        <img src="{{ item.image | relative_url }}" alt="{{ item.brand }} {{ item.model }}" loading="lazy" style="width:100%;height:auto;display:block;aspect-ratio:4/3;object-fit:cover;">
      {% endif %}
      {% if status == 'sold' %}
        <div style="position:absolute;inset:0;background:rgba(0,0,0,.4);display:flex;align-items:center;justify-content:center;">
          <span style="background:#fff;padding:6px 10px;border-radius:999px;font-weight:700;">SOLD</span>
        </div>
      {% endif %}
      {% if status != 'sold' and item.price %}
        <span style="position:absolute;top:8px;left:8px;background:#111;color:#fff;padding:4px 8px;border-radius:999px;font-weight:600;">€{{ item.price }}</span>
      {% endif %}
    </div>

    <div style="padding:12px;display:flex;flex-direction:column;gap:6px;">
      <h3 style="margin:0;font-size:1rem;">{{ item.brand }} — {{ item.model }}</h3>
      <p style="margin:0;color:#374151;font-size:.9rem;">
        Size: <strong>{{ item.size }}</strong> · Color: <strong>{{ item.color }}</strong>
        {% if item.condition %} · Condition: <strong>{{ item.condition }}</strong>{% endif %}
      </p>

      {% if item.notes %}
        <p style="margin:0;color:#6b7280;font-size:.9rem;">{{ item.notes }}</p>
      {% endif %}

      <div style="margin-top:8px;">
        {% if status == 'sold' %}
          <span style="font-weight:700;color:#b91c1c;">Sold</span>
        {% else %}
          {% assign mailto = item.contact | default: 'hello@kimono.guru' %}
          <a href="mailto:{{ mailto | uri_escape }}?subject={{ item.brand | uri_escape }}%20{{ item.model | uri_escape }}%20({{ item.size }})"
             style="display:inline-block;padding:8px 10px;border:1px solid #111;border-radius:10px;text-decoration:none;">Buy / Inquire</a>
        {% endif %}
      </div>
    </div>
  </article>
{% endfor %}
</div>

<p id="empty-state" style="display:none;color:#6b7280;margin-top:8px;">No kimonos match your filters.</p>

<!-- Tiny filtering script -->
<script>
(function () {
  const $ = (s, r=document) => r.querySelector(s);
  const $$ = (s, r=document) => Array.from(r.querySelectorAll(s));
  const cards = $$('.gi-card');
  const selSize = $('#filter-size');
  const selColor = $('#filter-color');
  const selStatus = $('#filter-status');
  const inputQ = $('#filter-q');
  const resetBtn = $('#reset-filters');
  const emptyState = $('#empty-state');

  // Init from URL
  const params = new URLSearchParams(location.search);
  if (params.has('size')) selSize.value = params.get('size').toLowerCase();
  if (params.has('color')) selColor.value = params.get('color').toLowerCase();
  if (params.has('status')) selStatus.value = params.get('status').toLowerCase();
  if (params.has('q')) inputQ.value = params.get('q');

  function applyFilters() {
    const fSize = selSize.value.trim();
    const fColor = selColor.value.trim();
    const fStatus = selStatus.value.trim();
    const q = inputQ.value.trim().toLowerCase();

    let visible = 0;
    cards.forEach(card => {
      const ok =
        (!fSize   || card.dataset.size   === fSize) &&
        (!fColor  || card.dataset.color  === fColor) &&
        (!fStatus || card.dataset.status === fStatus) &&
        (!q || card.dataset.text.includes(q));
      card.style.display = ok ? '' : 'none';
      if (ok) visible++;
    });

    emptyState.style.display = visible ? 'none' : '';
    const p = new URLSearchParams();
    if (fSize) p.set('size', fSize);
    if (fColor) p.set('color', fColor);
    if (fStatus) p.set('status', fStatus);
    if (q) p.set('q', q);
    history.replaceState(null, '', p.toString() ? `${location.pathname}?${p}` : location.pathname);
  }

  [selSize, selColor, selStatus].forEach(el => el.addEventListener('change', applyFilters));
  inputQ.addEventListener('input', applyFilters);
  resetBtn.addEventListener('click', () => {
    selSize.value = ''; selColor.value = ''; selStatus.value = ''; inputQ.value = '';
    applyFilters();
  });

  applyFilters();
})();
</script>
