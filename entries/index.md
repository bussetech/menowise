---
layout: page
title: Entries
eyebrow: Explore
description: "Journal-evidenced entries on menopause and midlife women's health, organized by topic, symptom, and intervention. Every entry cited; not medical advice."
permalink: /entries/
---

{% assign entries = "" | split: "" %}
{% for pair in site.data.sites %}{% assign entries = entries | push: pair[1] %}{% endfor %}
{% assign total = entries | size %}

**{{ total }}** cited {% if total == 1 %}entry{% else %}entries{% endif %}. Each is
resolved from per-source signals drawn from peer-reviewed literature and
clinical guidelines, with an honest evidence-strength label. See
[the datasets](/data/) for schemas, license, and provenance, and
[how to cite](/cite/).

{% include callout.html label="Not medical advice" body="Menowise aggregates and organizes what the published literature reports, with citations. It does not diagnose, advise, or recommend treatment. Talk to a qualified clinician about your own care." %}

{% assign kinds = "symptom,intervention,topic" | split: "," %}
{% for k in kinds %}
{% assign group = entries | where: "kind", k | sort: "title" %}
{% if group.size > 0 %}
## {{ k | capitalize }}s <span class="muted">({{ group.size }})</span>

{% for r in group -%}
- [**{{ r.title }}**](/entries/{{ r.id }}/) — evidence: {{ r.evidence_strength }}{% if r.status != "published" %} · {{ r.status }}{% endif %}
{% endfor %}
{% endif %}
{% endfor %}

---

<p class="muted">Evidence strength labels the study basis the cited sources
report (strong · moderate · limited · insufficient) and never overstates it.
Source agreement (confidence) is separate: how well independent sources concur.</p>
