---
title: Kimono Market
layout: default
---

<h1>Kimonos for Sale</h1>

<!-- Filters (no reset, no search) -->
<form id="filters" class="filters">
  <select id="filter-size" class="filter">
    <option value="">Size</option>
    {% assign sizes = site.data.kimonos | map:'size' | uniq | sort %}
    {% for s in sizes %}<option value="{{ s | downcase }}">{{ s }}</option>{% endfor %}
  </select>

  <select id="filter-color" class="filter">
    <option value="">Color</option>
    {% assign colors = site.data.kimonos | map:'color' | uniq | sort %}
    {% for c in colors %}<option value="{{ c | downcase }}">{{ c }}</option>{% endfor %}
  </select>

  <select id="filter-status" class="filter">
    <option value="">Any Status</option>
    <option value="available">Available</option>
    <option value="sold">Sold</option>
  </select>
</form>

<!-- Grid of tiles -->
<div id="grid" class="gi-grid">
{% for item in site.data.kimonos %}
  {% assign status = item.status | default: 'available' %}
  <article class="gi-card"
           data-size="{{ item.size | downcase }}"
           data-color="{{ item.color | downcase }}"
           data-status="{{ status | downcase }}">
    <div class="gi-media">
      {% if item.image %}
        <img class="gi-img" src="{{ item.image | relative_url }}" alt="{{ item.brand }} {{ item.model }}" loading="lazy">
      {% endif %}
      {% if status == 'sold' %}
        <div class="sold-overlay"><span>SOLD</span></div>
      {% endif %}
      {% if status != 'sold' and item.price %}
        <span class="price-badge">€{{ item.price }}</span>
      {% endif %}
    </div>

    <div class="gi-body">
      <h3 class="gi-title">{{ item.brand }} — {{ item.model }}</h3>
      <p class="gi-meta">
        Size: <strong>{{ item.size }}</strong> ·
        Color: <strong>{{ item.color }}</strong>
        {% if item.condition %} · Condition: <strong>{{ item.condition }}</strong>{% endif %}
      </p>

      {% if item.notes %}
        <p class="gi-notes">{{ item.notes }}</p>
      {% endif %}

      <div class="gi-cta">
        {% if status == 'sold' %}
          <span class="sold-label">Sold</span>
        {% else %}
          {% assign mailto = item.contact | default: 'hello@kimono.guru' %}
          <a class="btn" href="mailto:{{ mailto | uri_escape }}?subject={{ item.brand | uri_escape }}%20{{ item.model | uri_escape }}%20({{ item.size }})">Buy / Inquire</a>
        {% endif %}
      </div>
    </div>
  </article>
{% endfor %}
</div>

<p id="empty-state" class="empty-state" style="display:none;">No kimonos match your filters.</p>

<script>
(function () {
  const $ = s => document.querySelector(s);
  const $$ = s => Array.from(document.querySelectorAll(s));
  const cards = $$('.gi-card');
  const selSize = $('#filter-size');
  const selColor = $('#filter-color');
  const selStatus = $('#filter-status');
  const emptyState = $('#empty-state');

  // Default to 'available' unless URL overrides (?status=sold)
  const params = new URLSearchParams(location.search);
  if (params.has('size'))  selSize.value  = params.get('size').toLowerCase();
  if (params.has('color')) selColor.value = params.get('color').toLowerCase();
  selStatus.value = params.has('status') ? params.get('status').toLowerCase() : 'available';

  function applyFilters() {
    const fSize = selSize.value.trim();
    const fColor = selColor.value.trim();
    const fStatus = selStatus.value.trim();

    let visible = 0;
    cards.forEach(card => {
      const ok =
        (!fSize   || card.dataset.size   === fSize) &&
        (!fColor  || card.dataset.color  === fColor) &&
        (!fStatus || card.dataset.status === fStatus);
      card.style.display = ok ? '' : 'none';
      if (ok) visible++;
    });

    emptyState.style.display = visible ? 'none' : '';

    // Reflect filters in URL
    const p = new URLSearchParams();
    if (fSize)   p.set('size', fSize);
    if (fColor)  p.set('color', fColor);
    if (fStatus) p.set('status', fStatus);
    history.replaceState(null, '', p.toString() ? `${location.pathname}?${p}` : location.pathname);
  }

  [selSize, selColor, selStatus].forEach(el => el.addEventListener('change', applyFilters));
  applyFilters();
})();
</script>
