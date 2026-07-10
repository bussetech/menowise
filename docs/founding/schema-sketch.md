# Menowise — data design

The kdc mold, unchanged in shape: **per-source signals → canonical entries
with provenance and confidence**. Two validated dataset/schema pairs plus a
controlled-vocabulary entities file and a source registry. This replaces the
template's starter `records` dataset.

Layout:

```
data/
  signals/         # per-source signals (many small files)
  sites/           # canonical entries (the "sites" slot; here: entries)
  entities.yml     # controlled vocabulary of topics/symptoms/interventions
  sources.yml      # registered journals/indices (allowlist spine)
  index.md         # provenance + CC BY 4.0 statement (required)
schema/
  signals.schema.json
  sites.schema.json
data/profiles/
  scout.md         # gn_info_scout deployment adapter (domain sections)
  records.md       # gn_info_records deployment adapter (domain sections)
```

## Dataset: signals (`data/signals/*.yml`, YAML)

One record per source claim. Untrusted-provenance staging that records feeds on.

| field | type | constraints |
|-------|------|-------------|
| `id` | string | required, unique, slug (`sig-*`) |
| `source` | string | required, must key into `data/sources.yml` |
| `citation` | object | required: `{ doi?, pmid?, url, title, year }` — at least one stable identifier |
| `entry_hint` | string | optional; candidate canonical entry `id` (records decides) |
| `topic` | string | optional; must key into `entities.yml` if present |
| `claim` | string | required; the extracted claim, plainly stated |
| `study_type` | enum | required: `meta-analysis` \| `systematic-review` \| `rct` \| `cohort` \| `case-control` \| `cross-sectional` \| `narrative-review` \| `guideline` \| `other` |
| `sample_size` | integer | optional; present only where the source states it |
| `confidence` | enum | required: `high` \| `medium` \| `low` |
| `captured_at` | date | required (ISO 8601) |
| `notes` | string | optional; free text, fine-grained distinctions live here until they cluster |

Provenance: produced by `gn_info_scout` from allowlisted sources via
`scripts/source_fetch.py` (ADR-0025). Cadence: scheduled scout runs. No
transformation beyond extraction + confidence assignment.

## Dataset: entries (`data/sites/*.md` with YAML front matter)

Canonical, cited entries — the published unit. Negative outcomes are
first-class per GD-0004.

| field | type | constraints |
|-------|------|-------------|
| `id` | string | required, unique, slug |
| `title` | string | required |
| `kind` | enum | required: `topic` \| `symptom` \| `intervention` |
| `summary` | string | required; navigation-only, no advice |
| `evidence_strength` | enum | required: `strong` \| `moderate` \| `limited` \| `insufficient` — label states study type/size in body |
| `status` | enum | required: `published` \| `surveyed-thin` \| `surveyed-empty` — thin/empty are honest-zero content, not gaps (GD-0004) |
| `citations` | array | required when `status: published`; each item references a `signals` `id` and/or a `sources` `id` |
| `updated_at` | date | required (ISO 8601) |
| `notes` | string | optional |
| body (Markdown) | text | the entry prose; carries the honest evidence-strength discussion and the not-medical-advice framing |

Keys/relationships: `entries.citations[] → signals.id / sources.id`;
`entries.kind` and any `signals.topic` reference `entities.yml`. A
`surveyed-empty` entry with no citations is valid and expected where the
literature is thin.

Provenance: produced by `gn_info_records` from merged signals, chained on
`menowise-signals-merged`. Every published entry is cited to the literature.

## File: `data/entities.yml` (YAML)

Controlled vocabulary so topics/symptoms/interventions stay consistent.

| field | type | constraints |
|-------|------|-------------|
| `id` | string | required, unique, slug |
| `label` | string | required |
| `kind` | enum | required: `topic` \| `symptom` \| `intervention` |
| `aliases` | array[string] | optional |

## File: `data/sources.yml` (YAML)

The allowlist spine. Real journals/indices only; robots/ToS respected.

| field | type | constraints |
|-------|------|-------------|
| `id` | string | required, unique, slug |
| `name` | string | required (real journal/index name) |
| `type` | enum | required: `index` \| `journal` \| `guideline-body` |
| `url` | string | required |
| `allowlisted` | boolean | required; gates `scripts/source_fetch.py` |
| `access_notes` | string | optional; robots/ToS / rate constraints |

Assumption (brief silent on exact list): PubMed-class indices are the likely
spine; the initial `sources.yml` registers a small set of real, allowlisted
indices/journals at seeding time. Marked as an assumption for the seeding pass.

## Not-medical-advice invariant

Not a data field but a build invariant: a prominent not-medical-advice posture
renders on **every page** and in the About (theme config + content). Entries
are aggregation and navigation only — no diagnosis, no treatment
recommendation.
