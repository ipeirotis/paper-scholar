# Tasks

Feature tracker for the chapter-and-verse skill. Agents: read this before working
on the repo. New requests go under **Backlog** with the date they were filed;
when work lands, move the item to **Done** with the landing date and the files
that implement it. Items under **Proposed** are ideas awaiting the author's
approval — do not implement them unprompted.

## Done

- **2026-08 — Extract the scholar lane from blue-pencil into a standalone
  skill.** Core protocol carried over with blue-pencil couplings removed.
  (`SKILL.md`, `references/literature-checks.md`)
- **2026-08-14 — (a) Repo docs for agents and feature tracking.**
  (`AGENTS.md`, `tasks.md`)
- **2026-08-14 — (b) Archive the full text of every source read.** Cloud
  bucket (GCS/S3) when the project configures one and access verifies;
  otherwise `literature/sources/` in the host repo. SHA-256 recorded per file.
  (`references/source-archive.md`)
- **2026-08-14 — (c) Verifiable locators and the paywall flow.** Every source
  needs a DOI (cross-checked against its registration agency's metadata —
  Crossref, DataCite, or whichever agency issued it) or at minimum a stable
  public URL with access date. Legal open-access copies are searched first
  (publisher, Unpaywall, preprint servers); paywalled sources are requested
  from the author — download the PDF or print the page to PDF and place it in
  the store — never bypassed. (`references/source-archive.md`)
- **2026-08-14 — (d) Dated verification ledger in the host repo.** Results
  stored as append-only dated entries in `literature/verifications.md`, keyed
  by claim-text hash and source identity, with reuse rules so runs never
  redo settled work; pointed to from the host repo's `AGENTS.md`.
  (`references/verification-ledger.md`)

- **2026-08-14 — Rename the skill to chapter-and-verse.** "Give chapter and
  verse" = cite the exact authority for a claim; pairs with blue-pencil's
  idiom naming and avoids the crowded CiteCheck/refchecker namespace. The
  store env var became `LITERATURE_STORE` (rename-proof). Blue-pencil's
  dispatch command was renamed `/paper:scholar` → `/paper:verify-citations`
  (symmetric with `/paper:verify-numbers`). GitHub repo rename to
  `ipeirotis/chapter-and-verse` is done by the owner in repo settings; old
  URLs redirect.

## Proposed (awaiting approval)

- **Retraction and erratum check at verification time.** When verifying a
  citation, also query Crossref/Retraction Watch for retractions, errata, and
  expressions of concern; a supported claim resting on a retracted paper
  deserves a flag of its own. A narrow slice of this shipped with the ledger
  (2026-08-15, `references/verification-ledger.md`): *reusing* a DOI-backed
  entry checks the registrar's update relations (errata, corrigenda,
  retractions, replacements) before the old verdict is reused, because reuse
  correctness requires it. The full screen for newly verified citations
  remains proposed.
- **Bibliography metadata audit.** A cheap standalone pass: confirm every
  BibTeX entry's title/venue/year/DOI matches the real work via Crossref.
  Catches hallucinated or mangled references without full claim verification.
- **Version-of-record reconciliation.** When a claim was verified against a
  preprint, detect when the published version appears and prompt re-checking
  the claims whose evidence sat in sections that changed.
- **Machine-readable ledger companion.** Emit a JSONL alongside
  `verifications.md` if other tooling needs to consume results; the stable
  field labels make the Markdown greppable in the meantime.
- **Eval suite.** Test manuscripts with known-good, known-bad, and
  unverifiable citations to benchmark the skill's classifications and catch
  regressions (skill-creator eval loop), plus trigger-description
  optimization once behavior settles.

## Backlog

- **2026-08-15 — Sync the archive and ledger behaviors with blue-pencil's
  scholar lane.** The source archive, locator rules, verification ledger, and
  reuse rules landed here materially extend the protocol that blue-pencil's
  `/paper:verify-citations` also dispatches; the two copies of
  `literature-checks.md` now differ in substance. Decide with the author
  whether to upstream these behaviors into blue-pencil, keep them
  standalone-only, or have blue-pencil delegate to this skill — and record
  the decision here. Until then the implementations diverge deliberately,
  not silently.
- **2026-08-15 — Extract blue-pencil's analyst lane as the facts-and-figures
  skill.** Name approved by the author ("facts and figures": the reported
  numbers and the literal figures — mirrors chapter-and-verse's idiom form).
  Scope: carve `references/analysis-integrity.md` and the `paper-analyst`
  agent out of blue-pencil into a standalone repo
  (`ipeirotis/facts-and-figures`), couplings removed, with AGENTS.md/tasks.md
  scaffolding like this repo's. Blue-pencil's `/paper:verify-numbers`,
  `/paper:figures`, and `/paper:analyze` commands keep their names. Awaiting
  go-ahead to execute.
