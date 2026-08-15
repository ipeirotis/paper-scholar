# Agent guide: developing chapter-and-verse

This repository is a standalone agent skill for retrieval-grounded literature
checks: verifying that a manuscript's citations support the claims attached to
them, investigating uncited factual claims, and scanning stated contributions
for overlapping prior work — it gives chapter and verse for every claim it
checks. It was extracted from the scholar lane of
[blue-pencil](https://github.com/ipeirotis/blue-pencil) (whose dispatch
command is `/paper:verify-citations`, formerly `/paper:scholar`) so it can
ship and evolve independently of that editorial skill.

## Layout

- `SKILL.md` — entry point and trigger description; keep it short and route
  depth to `references/`.
- `references/literature-checks.md` — the core protocol: inventory claims,
  consult the ledger, verify citations, scan novelty, report.
- `references/source-archive.md` — locator (DOI/URL) requirements, legal
  full-text retrieval, and where archived copies go (bucket or repo folder).
- `references/verification-ledger.md` — the dated results ledger written into
  host repos, and its reuse rules.
- `agents/openai.yaml` — interface metadata for non-Claude agent platforms.
- `tasks.md` — the feature tracker (see below).

## Two repos to keep straight

This repo holds the skill. A **host repo** is the manuscript repository the
skill runs inside at verification time. Artifacts the skill creates at run
time — `literature/sources/`, `literature/verifications.md`, the pointer
section in the host `AGENTS.md` — belong to host repos and must never appear
here. Files here describe behavior; files there are that behavior's output.

## Working on the skill

- Read `tasks.md` before starting: it is the feature tracker. Record new
  feature requests there with a date; when work lands, move the item to Done
  with the date and the files that implement it.
- The `description` frontmatter in `SKILL.md` is the trigger — keep it in sync
  with capabilities whenever behavior changes.
- Preserve the integrity model in any edit: retrieved-not-remembered (a
  memory-cited source is fabrication), leads-not-verdicts for novelty,
  inaccessible ≠ unsupported, never edit the manuscript or bibliography, never
  bypass paywalls.
- Blue-pencil's `/paper:verify-citations` dispatches onto the same underlying protocol.
  Behavioral changes worth sharing across the two projects should be noted in
  `tasks.md` so they can be upstreamed or downstreamed deliberately rather
  than drifting apart silently.
