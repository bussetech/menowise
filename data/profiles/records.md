# menowise records profile — dataset configuration for gn_info_records

(Consumed as the `dataset_profile` input; platform `docs/gnome-evolution.md`.
This file defines the record schema surface, the status vocabulary, the id
rule, and the domain resolution guidance; the gnome's prompt defines the
resolution method. Record schema of record: `schema/sites.schema.json`.
menowise configures the generalized `gn_info_records` — no gnome code changed.

**Aggregation and navigation only. Entries never advise, diagnose, or
recommend treatment. Report what the literature shows, cited and honestly
labelled. Every published entry carries citations; not-medical-advice is a
site-wide invariant.**)

## The subject

One record per canonical entry: a single topic, symptom, or intervention.
Cluster signals whose `site_hint` names the same subject (match on the
topic/symptom/intervention name and its kind). A drug/intervention and the
symptom it is studied for are DISTINCT entries, cross-referenced in `notes:`;
facets of one subject (e.g. dosing detail) stay in one entry.

## Record schema fields (only these)

id, title, kind, summary, evidence_strength, status, sources[{url, title,
publisher, date, study_type, sample_size, note}], signals, confidence,
first_seen, last_updated, notes.

- `kind` ∈ {topic, symptom, intervention} — must match the subject's
  `entities.yml` entry kind.
- `summary`: a navigation-only synopsis of what the literature reports —
  plain, cited-in-substance, **never phrased as advice, diagnosis, or a
  treatment recommendation**. State uncertainty honestly.
- `sources`: the union of citation URLs from the signals used, verbatim
  (DOI/PMID/journal/guideline). Carry `study_type`/`sample_size` onto a
  source when its signals stated them — they feed `evidence_strength`.
  **`study_type` MUST be one of the schema enum values exactly** — map any
  prose the signals use onto: `meta-analysis`, `systematic-review` (incl.
  scoping reviews), `rct`, `cohort` (incl. "cohort study"), `case-control`,
  `cross-sectional` (incl. surveys), `narrative-review` (incl. generic
  "review"), `guideline` (incl. position/consensus statements), or `other`.
  Never emit free-text like "clinical guideline" — data CI rejects it.
- `signals`: the ids of every consumed signal.

## evidence_strength rubric (resolve from the clustered signals)

Assign from the strongest concordant evidence among the cited signals:

- `strong` — concordant meta-analyses / systematic reviews of controlled trials.
- `moderate` — one or more RCTs, without meta-analytic confirmation or with
  minor unresolved conflict.
- `limited` — observational evidence only (cohort/case-control/cross-sectional),
  or single small trials.
- `insufficient` — guideline/expert opinion or narrative review only, or
  sparse/conflicting evidence.

`evidence_strength` (study quality) is distinct from `confidence` (how well
sources agree). Both are honest; never inflate either.

## Status vocabulary

`published | surveyed-thin | surveyed-empty`.

- `published` — an entry with adequate cited literature; requires `summary`,
  `evidence_strength`, ≥1 source, ≥1 signal (schema-enforced).
- `surveyed-thin` — the subject was surveyed and the literature is sparse:
  keep the entry with what little is cited, `evidence_strength: insufficient`,
  and say so in `summary`/`notes`. Honest-zero content (GD-0004), not a gap.
- `surveyed-empty` — surveyed, no adequate literature found: a valid entry
  with no sources, documenting the absence. Honest-zero content.

Negative/null study results are recorded as evidence within an entry (in
`summary` and the cited signals), never dropped.

## Entity-typed fields

The subject maps to a `data/entities.yml` id (matched by `kind` + label /
aliases). Propose additions in run notes; never invent an id.

## The id rule (deterministic; collision clause at the end)

Build the id as a slug of the subject's distinctive name, lowercase, ASCII,
hyphenated:

- Drop generic filler words ("the", "in", "of", "for", "menopause",
  "menopausal" when they are not the distinguishing term).
- Prefer the established clinical term when the literature uses one
  (e.g. `vasomotor-symptoms`, `genitourinary-syndrome-menopause`,
  `mht-vasomotor` for an intervention-for-symptom entry).
- For an intervention-for-a-symptom entry, form `<intervention>-<symptom>`
  (e.g. `vaginal-estrogen-gsm`).
- **Reuse:** if an entry for this subject already exists, reuse its `id`
  exactly — never re-slug.
- **Collision clause:** if the derived id would name a *different* existing
  entry, append `-<kind>`, then `-2`, `-3` — the sole allowed disambiguation.
