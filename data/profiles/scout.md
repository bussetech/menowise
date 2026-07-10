# menowise scout profile — dataset configuration for gn_info_scout

(Consumed as the `dataset_profile` input; platform `docs/gnome-evolution.md`.
This file defines WHAT the dataset catalogs and WHICH attributes are
signal-worthy; the gnome's prompt defines HOW (the signal shape is frozen —
see `schema/signals.schema.json`). menowise is info-archetype build 2; this
profile configures the generalized `gn_info_scout`, no gnome code changed.)

## The dataset

Journal-evidenced information on **menopause and midlife women's health**:
the perimenopause–postmenopause transition, its symptoms, the interventions
studied for them, and the surrounding clinical concepts. The
**subject-of-record ("site") is one canonical entry** — a single topic,
symptom, or intervention (e.g. "vasomotor symptoms", "vaginal estrogen for
genitourinary syndrome of menopause", "cognitive behavioural therapy for
menopausal symptoms"). A drug class and a specific drug are distinct
subjects only when the literature treats them distinctly; facets of one
subject are one subject. `site_hint` format for unknown subjects:
`"<topic/symptom/intervention as reported> — <kind: topic|symptom|intervention>"`.

**Aggregation and navigation only. This dataset does not advise, diagnose,
or recommend treatment. Record what the literature reports; never phrase a
signal as guidance to a reader.**

## Attribute vocabulary

Lowercase snake keys — the only attributes a signal may use:

`definition`, `prevalence`, `population`, `intervention`, `comparator`,
`outcome`, `effect`, `effect_size`, `study_type`, `sample_size`, `duration`,
`safety`, `contraindication`, `guideline_recommendation`, `certainty`,
`mechanism`, `conflict`.

- `study_type` value ∈ {meta-analysis, systematic-review, rct, cohort,
  case-control, cross-sectional, narrative-review, guideline, other} — the
  cited work's design, verbatim as the source states it.
- `sample_size` value = the enrolled/analysed n the source states (digits).
- `effect` / `effect_size` carry the reported result and its magnitude
  (e.g. "reduced weekly hot-flush frequency by ~75%", "RR 0.46 [0.29–0.74]").
- `guideline_recommendation` records what a named guideline body states,
  attributed — it is a *report of* a recommendation, not menowise advising.

## What is signal-worthy

Findings from peer-reviewed literature and reputable clinical guidelines
about a subject: what an intervention was studied for and what it showed
(including **null and negative results — those are signals, not omissions**),
study design and size, safety/contraindication findings, prevalence and
definitions, and guideline positions. One claim per source per attribute;
when sources differ on the same attribute, split into separate signals per
source — never merge or average at intake.

Skip: lay-press health stories with no cited study, opinion/commentary,
product marketing, anecdote, and anything phrased as advice to individuals.
A source with no stable citation identifier (DOI/PMID/journal URL) yields no
signal — provenance is the citation.

**Cap: the most informative ~8 signals per subject.** Fewer careful,
well-cited signals beat broad thin coverage (health restraint).

## Confidence refinement (source-level trust)

Primary/high: peer-reviewed primary studies and systematic reviews in
indexed journals, and named guideline bodies (The Menopause Society/NAMS,
NICE, ACOG, the Endocrine Society, Cochrane). Medium: reputable secondary
summaries of specific studies. Low: lay aggregators, headline-only items,
or material flagged unverified. `confidence` is SOURCE trust only — study
quality is resolved into the entry's `evidence_strength` by the records
gnome, not here.
