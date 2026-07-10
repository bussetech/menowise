# Menowise — gnome roster

## New gnomes: none

Menowise creates **no project-specific gnomes** and **no per-repo knoll**.
This is deliberate and correct post-ADR-0045: the `info` archetype's work is
carried by the platform-homed product pair, already in the platform `info`
knoll.

## Deployed (not new) — config-only deployment adapters

| gnome | deployment mechanism | trigger | input_trust | purpose |
|-------|----------------------|---------|-------------|---------|
| `gn_info_scout` (v1.0.1, `info` knoll, home `platform`) | add `menowise` to `deployments:`; wrapper `gn-menowise-scout.yml`; `data/profiles/scout.md` | schedule / manual dispatch; emits `menowise-signals-merged` on merge | untrusted (reads external literature via the ADR-0025 allowlist; never fetches directly) | Read allowlisted journals/indices, emit per-source signals with provenance + confidence for the menopause/midlife-health domain. |
| `gn_info_records` (v1.0.1, `info` knoll, home `platform`) | add `menowise` to `deployments:`; wrapper `gn-menowise-records.yml`; `data/profiles/records.md` | `repository_dispatch: menowise-signals-merged` (chained) | untrusted (reads in-repo signal prose as content) | Canonicalize signals into cited entries carrying evidence-strength labels; keep honest zeros as content. |

Deployment is proven **first** with a `fixtures/projects/menowise/` pack and a
`--fixtures` dry-run before any live run (brief).

## What would have to become true to earn a new gnome

A new Menowise gnome (and only then, a `menowise` knoll) would be justified
only if a *judgment* task appears that the `info` pair cannot express through
its profiles — e.g. a distinct editorial-review pass whose judgment differs
genuinely from records' canonicalization. If the evidence-strength label
turns out to need prompt-level changes, the correct move is to **generalize
the `info` pair** (protocol step c, a new profile-fed input) — recorded as an
EPIC4-03 finding — **not** to fork a Menowise-specific gnome. Symptom-checking
and personalization are explicitly out of scope this build and would not
justify a gnome even if they were in scope (health restraint overrides growth).

## Knoll verdict

**No project knoll.** The roster of new gnomes is empty, so by the default
rule there is no per-repo knoll. The two deployed gnomes draw their shared
knowledge from the **platform `info` knoll**, which they already belong to.
Menowise must not spawn a `menowise` knoll.
