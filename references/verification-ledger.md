# Verification ledger: dated, reusable results

Retrieval is the expensive part of this skill, and manuscripts get checked many
times as they evolve. The ledger records every verification as a dated entry in
the host repo so a later run — by this skill, a coauthor, or another agent —
can see what has already been established and against exactly which text,
instead of redoing the work. It is also the audit trail: a verdict without a
date and an archived source behind it is just an assertion.

## Location and discovery

- The ledger lives at `literature/verifications.md` in the host repo root,
  beside the `literature/sources/` archive folder.
- The host repo's `AGENTS.md` (or `CLAUDE.md` when that is what the repo uses)
  should carry a short "Literature verification" section pointing at the
  ledger and the source store, so any agent working in that repo finds them.
  Create or refresh that pointer when writing the ledger, and say so in the
  report — the manuscript and bibliography are never touched, but this
  infrastructure edit is announced, not silent. If the repo has no agent
  instructions file at all, propose creating one in `Author decisions` rather
  than inventing repo conventions unasked.

## Ledger format

Header, once per file:

```markdown
# Literature verifications

store: literature/sources/        <!-- or gs://bucket/prefix, s3://bucket/prefix -->
maintained-by: chapter-and-verse skill
```

One entry per verification, newest appended last, with these exact field
labels so entries stay greppable by humans and machines alike:

```markdown
## [2026-08-14T22:31Z] cite:smith2021 — introduction.tex:41
claim: "Smith et al. show that crowdsourced labels reach expert accuracy at one tenth the cost."
claim-hash: ab12cd34ef56
source: doi:10.1145/1234567.1234568
version-read: version of record
archived: literature/sources/smith2021--10.1145_1234567.1234568.pdf sha256:9f2a…
verdict: partially supported
evidence: "labels reached expert agreement on 3 of 5 tasks" (sec. 5.1) — cost claim not addressed
notes: cost figure may come from a different paper; asked author.
```

- `claim-hash` is the first 12 hex characters of the SHA-256 of the claim
  sentence with runs of whitespace collapsed to single spaces and case
  preserved — a case change can be a different claim (a gene name, a
  mathematical symbol, a unit), so case is never normalized away. Hashing the
  text rather than keying on file and line keeps entries valid when sections
  move or files are renamed.
- `version-read` distinguishes the version of record from a preprint or an
  abstract-only read; a verdict earned on a preprint does not silently carry
  over to the published version.
- Novelty scans are logged too, since they age and their scope matters. Each
  lead carries the same source-identity, archive, and evidence fields as a
  citation entry — a lead a later run cannot reopen and re-read is not
  reusable evidence:

```markdown
## [2026-08-14T22:47Z] novelty — "we are the first to model X under Y"
searched: <the queries and databases used>
lead: doi:10.1234/abcd — Doe 2023, "Modeling X under Y-like constraints"
  version-read: version of record
  archived: literature/sources/doe2023--10.1234_abcd.pdf sha256:4e7b…
  evidence: "we model X under Y'-constraints using…" (sec. 3) — same setting, different estimator
leads-found: 1; nothing else on point (state this explicitly when a search comes up empty)
```

Outcomes with no retrievable source still get entries — with explicit
sentinels, never invented evidence, because "could not check" and "checked
and unsupported" must stay distinguishable forever:

```markdown
## [2026-08-15T08:40Z] cite:jones2019 — related.tex:12
claim: "…"
claim-hash: 77ab19c02d3e
source: unresolved — key maps to no work in the bibliography
version-read: none
archived: none
verdict: unverifiable
evidence: none — nothing was retrieved; asked the author which work this key denotes
```

A zero-lead novelty scan likewise records `lead: none — searches returned
nothing on point` under its `searched:` line: the absence of a found overlap
is itself a dated finding, distinct from a scan that never ran.

## Reuse rules

Consult the ledger after the claim inventory is pinned and before the first
search. A claim's latest entry is reusable when all of these hold:

- the claim text still hashes to the recorded `claim-hash`;
- the bibliography still resolves the citation to the same work: the same
  DOI, or for DOI-less sources the same canonical URL as recorded — the
  archived copy's integrity is the separate check below, not the
  bibliography's identity;
- the recorded `version-read` is still the best text reachable: an entry
  earned on a preprint or an abstract-only read is retried, not reused, once
  a fuller text (the version of record, the full paper) may be available — an
  unchanged DOI never carries a preprint verdict onto the published version;
- the archived copy still exists at its recorded path (or in the configured
  store) and matches its recorded SHA-256 — a verdict whose exact text can no
  longer be reopened is not reusable. When the store cannot be checked this
  run, the prior verdict may still be reported as dated history, but say the
  archive went unverified rather than presenting the entry as reused;
- the recorded verdict is supported, partially supported, or unsupported — an
  unverifiable verdict is always worth retrying, since access may have improved.

A reused verdict enters the citation audit with its original date, marked as
reused; it is never presented as fresh work. Re-verify regardless of the ledger
when the author asks, when the claim or bibliography entry changed, or when the
source was a URL snapshot and the claim is sensitive to the live page having
changed.

Novelty scans go stale in a way citation checks do not — the literature moves.
Treat a novelty entry older than about six months as a starting point for a
fresh scan, not a current answer, and state the scan date whenever one is
reused. Age is not the only predicate: reuse a novelty entry only when the
manuscript's novelty claim still reads as the recorded one and the entry's
`searched:` scope covers what the current request asks. A reworded
contribution, a new database, or a broadened boundary gets a fresh scan
whatever the entry's age — an old answer to a different question is not a
current answer to this one.

## Append, never rewrite

Entries are appended, and past entries are never edited or deleted: the
history of what was checked, when, and against which text is the point. When a
claim is re-verified, the newer entry supersedes the older by its heading
timestamp — headings carry date and time (UTC) precisely so that same-day
entries cannot tie ambiguously. If parallel runs both appended and the file
conflicts, keep both sets of entries in timestamp order; should two entries
for the same claim still carry the same timestamp with different verdicts,
neither is reusable — report the conflict to the author instead of silently
picking one.
