# Menowise — data design

The kdc mold, unchanged in shape: **per-source signals → canonical entries
with provenance and confidence**. Two validated dataset/schema pairs plus a
controlled-vocabulary entities file and a source registry. This replaces the
template's starter `records` dataset.

> **Correction (buildout, 2026-07-10) — of-record ≠ true.** This sketch was
> drafted to the *domain ideal*, before the `info` pair's **frozen output
> contract** was checked against the actual gnome prompts. `gn_info_scout`
> emits a **fixed signal skeleton** (`attribute`/`value`/`source_url`/
> `site_id`/… — see the prompt's strict output block); it **cannot** emit the
> `claim`/`citation`/`study_type` fields this sketch first proposed. The domain
> is expressed by mapping onto the frozen shape via the profile's *attribute
> vocabulary* (study_type/sample_size as attributes; DOI/PMID/journal as
> `source_url`/`publisher`), not by a bespoke signal schema. The **authoritative
> field definitions are the checked-in `schema/*.json`**; the tables below are
> corrected to match them. This is config-only — zero gnome code — proven by a
> `--fixtures` dry-run + direct schema validation (menowise buildout PR).

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

## Dataset: signals (`data/signals/*.yml`, YAML) — FROZEN archetype shape

One record per source claim. Untrusted-provenance staging that records feeds
on. **Emitted verbatim by `gn_info_scout`; the fields are the archetype
contract (ADR-0045), identical in shape to kdc's.** The menopause domain lives
in *which* attributes are signal-worthy (the scout profile's vocabulary), not
in the field set. Authoritative: `schema/signals.schema.json`.

| field | type | constraints |
|-------|------|-------------|
| `id` | string | required, unique, `sig-YYYYMMDD-<slug>`, = filename stem |
| `site_id` | string\|null | ref `data/sites/<id>.yml`; null while unmatched |
| `site_hint` | string | subject as reported (omit when `site_id` set) |
| `attribute` | string | required; from the profile's vocabulary (`study_type`, `sample_size`, `effect`, `guideline_recommendation`, `population`, …) |
| `value` | string | required; the claim raw, as reported |
| `source_url` | string | required; the **citation** URL verbatim (DOI/PMID/journal/guideline) |
| `source_title` / `publisher` / `source_date` | string | article title / journal or body / year |
| `observed_date` | date | required; run date |
| `collected_by` | string | required; `gn_info_scout/<version>` |
| `confidence` | enum | required: `high`\|`medium`\|`low` — **source-level trust**, NOT study-evidence strength |
| `notes` | string | optional |

So a study's design/size are **attribute signals** (`attribute: study_type`,
`value: "rct"`; `attribute: sample_size`, `value: "245"`); the citation is the
`source_url`+`publisher`+`source_date` provenance. Evidence-strength labelling
is resolved onto the *entry* by records, not the signal.

Provenance: produced by `gn_info_scout` from allowlisted sources via
`scripts/source_fetch.py` (ADR-0025). No transformation beyond extraction +
source-confidence assignment.

## Dataset: entries (`data/sites/*.yml`, YAML) — records-emitted

Canonical, cited entries — the published unit. Negative outcomes are
first-class per GD-0004.

Domain fields are profile-defined; the **frozen structural fields** (`sources`,
`signals`, `confidence`, `first_seen`, `last_updated`) are what the records
gnome emits and must be present. Authoritative: `schema/sites.schema.json`.

| field | type | constraints |
|-------|------|-------------|
| `id` | string | required, unique, slug, = filename stem (deterministic per profile id rule) |
| `title` | string | required |
| `kind` | enum | required: `topic` \| `symptom` \| `intervention` (keys `entities.yml`) |
| `summary` | string | navigation-only, no advice; **required when `published`** |
| `evidence_strength` | enum | `strong`\|`moderate`\|`limited`\|`insufficient`; **required when `published`**; resolved from cited signals' study_type/size per the profile rubric |
| `status` | enum | required: `published` \| `surveyed-thin` \| `surveyed-empty` — thin/empty are honest-zero content (GD-0004) |
| `sources` | array | the cited literature (`{url, title?, publisher?, date?, study_type?, sample_size?, note?}`); **required, ≥1, when `published`** |
| `signals` | array | consumed signal ids; **required, ≥1, when `published`** |
| `confidence` | enum | required: `high`\|`medium`\|`low` — source *agreement* (not evidence_strength) |
| `first_seen` / `last_updated` | date | required (ISO 8601) |
| `notes` | string | optional; conflicts, scope caveats, why the literature is thin |

Keys/relationships: `entries.sources[].url` are the verbatim citation URLs from
`entries.signals[] → signals.id`; `entries.kind`/subject reference
`entities.yml`. **The not-medical-advice invariant is schema-enforced:** a
`published` entry with no `sources`/`signals` (or missing `summary`/
`evidence_strength`) fails validation. A `surveyed-empty` entry with no
citations is valid and expected where the literature is thin. Entries are
`.yml` (records' output path) — the site renders pages from them; there is no
per-entry Markdown body (records emits only schema fields).

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
