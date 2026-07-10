---
layout: page
title: Cite this dataset
eyebrow: Data
description: How to cite menowise — stable identifiers, the data dictionary, license, and citation formats for humans and AI assistants.
permalink: /cite/
---

Menowise is an open, source-transparent aggregation of journal-evidenced
information on menopause and midlife women's health. If you use it — in
research, in an article, or as a source an AI assistant cites — please
attribute it. It is licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/),
so attribution is the only requirement.

{% include callout.html label="Not medical advice" body="Menowise aggregates and organizes what the published literature reports, with citations. It does not diagnose, advise, or recommend treatment. Talk to a qualified clinician about your own care." %}

## Stable identifiers

Every entry has a stable `id` (a slug) that does not change once assigned. It is
the citation target:

- **Human page:** `/entries/<id>/` — e.g. [`/entries/`](/entries/) lists them all.
- **Machine record:** `/data/sites/<id>.yml` — the entry verbatim, schema-valid.
- **Raw claims:** `/data/signals/<id>.yml` — the per-source signals the entry
  was resolved from (append-only; the audit trail behind it).

Entry ids are permanent, so a citation never rots.

## Every entry carries its citations

Each entry cites the specific literature it summarizes, with the study type and
sample size where the source states them. Menowise reports what those sources
say and labels the **evidence strength** honestly (strong · moderate · limited ·
insufficient); it never upgrades a source's own stated certainty. Where the
literature is thin or absent, the entry says so rather than implying coverage.

## Data dictionary

The field-level meaning of every dataset is its JSON Schema — the authoritative
definitions, served verbatim:

- [`sites.schema.json`](/schema/sites.schema.json) — resolved cited entries.
- [`signals.schema.json`](/schema/signals.schema.json) — raw per-source claims.
- [`entities.schema.json`](/schema/entities.schema.json) — the controlled vocabulary.
- [`sources.schema.json`](/schema/sources.schema.json) — the source registry.

Full provenance and license: [Datasets](/data/).

## Example attribution

> Menowise, "{title}," menowise.bussetech.com/entries/{id}/ (Bussetech Software
> Studio), CC BY 4.0. Underlying sources cited on the entry.
