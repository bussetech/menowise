---
layout: page
title: Datasets
eyebrow: Data
description: The datasets behind menowise — text-based, versioned, schema-validated. Aggregation and navigation only; not medical advice.
permalink: /data/
---

Menowise aggregates **journal-evidenced** information on menopause and midlife
women's health. Every dataset is a text file in the repo (`data/`), validated
in CI against a JSON Schema (`schema/`), and served here verbatim.

**Not medical advice.** These datasets aggregate and organize what the
published literature reports, with citations and honest evidence-strength
labels. They do not diagnose, advise, or recommend treatment.

## Datasets

| dataset | files | schema |
| --- | --- | --- |
| signals (per-source claims) | [`signals/`](/data/signals/) | [`signals.schema.json`](/schema/signals.schema.json) |
| entries (canonical, cited) | [`sites/`](/data/sites/) | [`sites.schema.json`](/schema/sites.schema.json) |
| entities (controlled vocabulary) | [`entities.yml`](/data/entities.yml) | [`entities.schema.json`](/schema/entities.schema.json) |
| sources (fetch allowlist) | [`sources.yml`](/data/sources.yml) | [`sources.schema.json`](/schema/sources.schema.json) |

The model is the info archetype's, unchanged in shape: **per-source signals →
canonical entries with provenance and confidence** (kdc is build 1). Field
detail: [`founding/schema-sketch.md`](https://github.com/bussetech/menowise/blob/main/docs/founding/schema-sketch.md).

## License

The datasets published by this project are licensed under
[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) (studio default,
ADR-0002). The project's code is MIT-licensed (see the repo's LICENSE).

## Provenance

- **Where the data comes from:** peer-reviewed biomedical literature and
  reputable clinical guidelines, registered in [`sources.yml`](/data/sources.yml)
  (the fetch allowlist, ADR-0025). Every published entry cites its sources.
- **How it is collected:** `gn_info_scout` extracts per-source *signals*
  (one claim per source) from allowlisted literature; `gn_info_records`
  clusters signals into canonical *entries* with an honest `evidence_strength`
  label. Both are studio agents; every change arrives as a human-merged PR.
- **Transformations:** extraction and clustering only — no averaging of
  conflicting claims, no inference beyond what sources state. Null and
  negative results are recorded, not dropped.
- **Current state:** the datasets are **empty pending the seeding pass**
  (careful, cited entries; menowise#4). Honest-zero entries
  (`surveyed-thin`/`surveyed-empty`) are content, not gaps (GD-0004).
