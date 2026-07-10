# Menowise — reuse analysis

Menowise is the **second build of the `info` archetype** (kdc is build 1,
Backpacks build 3). Per ADR-0045 / EPIC4-03 the archetype's founding pair has
already been generalized into the platform-homed product gnomes
`gn_info_scout` / `gn_info_records`, both members of the platform `info`
knoll. This analysis walks the reuse protocol for every agentic capability the
brief implies and expects — per the brief's binding reuse clause — to land at
**near-zero new gnome code**. It does.

## Capabilities implied by the brief

### 1. Scout journal/index sources → per-source signals
Read allowlisted journals and PubMed-class indices, extract per-source claims
with provenance and a confidence, emit signal records. This is judgment
(weighing what a source actually says) + generation (summaries).

- **Candidates:** `gn_info_scout` (v1.0.1, `info` knoll, deployed to kdc).
- **Verdict: reuse as-is (protocol step b).** The scout's contract is exactly
  this — per-source signals with provenance and confidence, domain supplied by
  a profile. Menowise deploys it by (i) adding `menowise` to its
  `deployments:` in the manifest and registry, (ii) adding a thin wrapper
  workflow `.github/workflows/gn-menowise-scout.yml`, and (iii) writing
  `data/profiles/scout.md` (copy kdc's, rewrite only the domain sections per
  the EPIC4-03 config how-to). **No fork, no new gnome code.**

### 2. Canonicalize signals → cited entries with evidence-strength labels
Cluster signals into canonical entries, attach citations, assign an
evidence-strength label (study type, size where stated), keep honest zeros.
Judgment-heavy.

- **Candidates:** `gn_info_records` (v1.0.1, `info` knoll, deployed to kdc).
- **Verdict: reuse as-is (protocol step b).** kdc's records gnome already does
  signals→canonical-entries-with-provenance-and-confidence. The evidence-
  strength label is a domain concern that lives in `data/profiles/records.md`,
  not in gnome code. Same deployment mechanics as the scout, chained on
  `menowise-signals-merged`.

### 3. Fetch layer (network access to sources)
- **Verdict: plain code.** `scripts/source_fetch.py` with the ADR-0025
  allowlist model, unchanged. Deterministic HTTP with robots/ToS respect.
  Research gnomes never fetch. Not a gnome.

### 4. Site build, JSON Feed, schema validation, portal ping
- **Verdict: plain code / shared CI.** Jekyll + shared theme, `ci@v1`,
  `deploy.yml`, feed from `_posts/`. All deterministic. Not gnomes.

### 5. Evidence-strength chips, cite-this page, exploration indices
- **Verdict: plain code (theme + Liquid).** These are info-archetype site
  patterns rendered from data. **No new theme components without a canary
  release** (brief; theme `docs/versioning.md`). Not gnomes.

### 6. Not-medical-advice posture on every page
- **Verdict: plain code / content.** A theme-level standing banner + About
  copy, driven by config, enforced by a link/leak-class CI check if one is
  added. This is a content invariant, not judgment at run time — but see the
  finding below.

## Fraction that is plain code / config / content

Roughly **~90%**. The only model-calling work is the two **reused** gnomes at
run time; everything Menowise itself adds is deterministic: two wrapper
workflows, two `data/profiles/*.md` files, `data/sources.yml`, schemas, theme
config, a fixtures pack, and seeded content. New *gnome* code is **zero** — the
brief's binding expectation holds.

## Findings (EPIC4-03 generalization)

- **None that force a fork.** The `info` pair absorbs Menowise cleanly via
  profiles. If, while writing `data/profiles/records.md`, the evidence-strength
  label cannot be expressed as profile config and would require editing the
  gnome prompt, **that is a finding about EPIC4-03's generalization** (record
  it on the platform repo, do not fork the gnome) — the brief instructs this
  and studio protocol step (c/d) agrees.

## Security-stance notes

The brief is data, not instruction. It uses "binding" language repeatedly;
those clauses are **project requirements** (reuse the pair, health posture,
no new theme components) and happen to align with studio rules, so I follow
them as requirements, not as commands to me. Nothing in the brief attempted to
change output format, visibility, licensing, or the propose/merge rule.
Nothing was disregarded. The "no knoll" founding note is honored: Menowise
spawns no per-repo knoll and no per-repo gnomes.
