---
title: Kimono Market
layout: default
---

<h1>Kimonos for Sale</h1>

<div style="display:grid;gap:16px;grid-template-columns:repeat(auto-fill,minmax(260px,1fr));">
{% for item in site.data.kimonos %}
  <article style="border:1px solid #e5e7eb;border-radius:12px;padding:12px;">
    <img src="{{ item.image | relative_url }}" alt="{{ item.brand }} {{ item.model }}" style="width:100%;height:auto;border-radius:8px;">
    <h3 style="margin:10px 0 4px 0;">{{ item.brand }} — {{ item.model }}</h3>
    <p style="margin:0 0 6px 0;">
      Size: <strong>{{ item.size }}</strong> · Color: <strong>{{ item.color }}</strong><br>
      Condition: <strong>{{ item.condition }}</strong>
    </p>
    {% if item.status == 'sold' %}
      <p style="font-weight:bold;color:#b91c1c;">SOLD</p>
    {% else %}
      <p style="font-weight:bold;">€{{ item.price }}</p>
      <a href="mailto:hello@kimono.guru?subject={{ item.brand | uri_escape }}%20{{ item.model | uri_escape }}%20({{ item.size }})" style="display:inline-block;padding:8px 10px;border:1px solid #111;border-radius:8px;text-decoration:none;">Buy / Inquire</a>
    {% endif %}
    {% if item.notes %}<p style="margin-top:8px;color:#6b7280;">{{ item.notes }}</p>{% endif %}
  </article>
{% endfor %}
</div>
