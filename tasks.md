# Tasks

Feature tracker for the paper-scholar skill. Agents: read this before working
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
  needs a DOI (cross-checked against Crossref) or at minimum a stable public
  URL with access date. Legal open-access copies are searched first
  (publisher, Unpaywall, preprint servers); paywalled sources are requested
  from the author — download the PDF or print the page to PDF and place it in
  the store — never bypassed. (`references/source-archive.md`)
- **2026-08-14 — (d) Dated verification ledger in the host repo.** Results
  stored as append-only dated entries in `literature/verifications.md`, keyed
  by claim-text hash and source identity, with reuse rules so runs never
  redo settled work; pointed to from the host repo's `AGENTS.md`.
  (`references/verification-ledger.md`)

## Proposed (awaiting approval)

- **Retraction and erratum check.** When verifying a citation, also query
  Crossref/Retraction Watch for retractions, errata, and expressions of
  concern; a supported claim resting on a retracted paper deserves a flag of
  its own.
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

(nothing filed)
