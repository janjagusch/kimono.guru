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
      {% assign sizes = site.data.kimonos | map: 'size' | uniq | sort %}
      {% for s in sizes %}
        <option value="{{ s | downcase }}">{{ s }}</option>
      {% endfor %}
    </select>
  </label>

  <label>
    <span>Color</span>
    <select id="filter-color">
      <option value="">Any</option>
      {% assign colors = site.data.kimonos | map: 'color' | uniq | sort %}
      {% for c in colors %}
        <option value="{{ c | downcase }}">{{ c }}</option>
      {% endfor %}
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

<!-- Table -->
<div style="overflow:auto;">
<table id="kimono-table" style="width:100%;border-collapse:collapse;min-width:720px;">
  <thead>
    <tr>
      <th style="text-align:left;border-bottom:1px solid #e5e7eb;padding:8px;">Photo</th>
      <th style="text-align:left;border-bottom:1px solid #e5e7eb;padding:8px;">Brand / Model</th>
      <th style="text-align:left;border-bottom:1px solid #e5e7eb;padding:8px;">Size</th>
      <th style="text-align:left;border-bottom:1px solid #e5e7eb;padding:8px;">Color</th>
      <th style="text-align:left;border-bottom:1px solid #e5e7eb;padding:8px;">Condition</th>
      <th style="text-align:left;border-bottom:1px solid #e5e7eb;padding:8px;">Price</th>
      <th style="text-align:left;border-bottom:1px solid #e5e7eb;padding:8px;">Notes</th>
    </tr>
  </thead>
  <tbody>
  {% for item in site.data.kimonos %}
    {% assign status = item.status | default: 'available' %}
    <tr class="gi-row"
        data-size="{{ item.size | downcase }}"
        data-color="{{ item.color | downcase }}"
        data-status="{{ status | downcase }}"
        data-text="{{ item.brand | append: ' ' | append: item.model | downcase }}"
        style="border-bottom:1px solid #f1f5f9;">
      <td style="padding:8px;">
        {% if item.image %}
          <img src="{{ item.image | relative_url }}" alt="{{ item.brand }} {{ item.model }}" style="max-width:120px;height:auto;border-radius:6px;">
        {% endif %}
      </td>
      <td style="padding:8px;">
        <strong>{{ item.brand }}</strong><br>
        <small>{{ item.model }}</small>
      </td>
      <td style="padding:8px;">{{ item.size }}</td>
      <td style="padding:8px;">{{ item.color }}</td>
      <td style="padding:8px;">{{ item.condition }}</td>
      <td style="padding:8px;">
        {% if status == 'sold' %}
          <strong style="color:#b91c1c;">SOLD</strong>
        {% else %}
          €{{ item.price }}
          {% if item.contact %}
            <div><a href="mailto:{{ item.contact | uri_escape }}?subject={{ item.brand | uri_escape }}%20{{ item.model | uri_escape }}%20({{ item.size }})">Inquire</a></div>
          {% else %}
            <div><a href="mailto:hello@kimono.guru?subject={{ item.brand | uri_escape }}%20{{ item.model | uri_escape }}%20({{ item.size }})">Inquire</a></div>
          {% endif %}
        {% endif %}
      </td>
      <td style="padding:8px;">{{ item.notes }}</td>
    </tr>
  {% endfor %}
  </tbody>
</table>
</div>

<p id="empty-state" style="display:none;color:#6b7280;margin-top:8px;">No kimonos match your filters.</p>

<!-- Filter logic -->
<script>
(function () {
  const $ = (s, r=document) => r.querySelector(s);
  const $$ = (s, r=document) => Array.from(r.querySelectorAll(s));
  const rows = $$('.gi-row');
  const selSize = $('#filter-size');
  const selColor = $('#filter-color');
  const selStatus = $('#filter-status');
  const inputQ = $('#filter-q');
  const resetBtn = $('#reset-filters');
  const emptyState = $('#empty-state');

  // Init from URL (?size=a2&color=blue&status=available&q=atama)
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
    rows.forEach(row => {
      const ok =
        (!fSize   || row.dataset.size   === fSize) &&
        (!fColor  || row.dataset.color  === fColor) &&
        (!fStatus || row.dataset.status === fStatus) &&
        (!q || row.dataset.text.includes(q));
      row.style.display = ok ? '' : 'none';
      if (ok) visible++;
    });

    emptyState.style.display = visible ? 'none' : '';

    // Update URL
    const p = new URLSearchParams();
    if (fSize) p.set('size', fSize);
    if (fColor) p.set('color', fColor);
    if (fStatus) p.set('status', fStatus);
    if (q) p.set('q', q);
    const newUrl = p.toString() ? `${location.pathname}?${p.toString()}` : location.pathname;
    history.replaceState(null, '', newUrl);
  }

  [selSize, selColor, selStatus].forEach(el => el.addEventListener('change', applyFilters));
  inputQ.addEventListener('input', applyFilters);
  resetBtn.addEventListener('click', () => {
    selSize.value = '';
    selColor.value = '';
    selStatus.value = '';
    inputQ.value = '';
    applyFilters();
  });

  applyFilters();
})();
</script>
