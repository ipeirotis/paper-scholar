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
## [2026-08-14] cite:smith2021 — introduction.tex:41
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
  sentence, lowercased with whitespace collapsed. Hashing the text rather than
  keying on file and line keeps entries valid when sections move or files are
  renamed.
- `version-read` distinguishes the version of record from a preprint or an
  abstract-only read; a verdict earned on a preprint does not silently carry
  over to the published version.
- Novelty scans are logged too, since they age and their scope matters:

```markdown
## [2026-08-14] novelty — "we are the first to model X under Y"
searched: <the queries and databases used>
leads: Doe 2023 (doi:…, sec. 3–4 overlap); nothing else on point
```

## Reuse rules

Consult the ledger after the claim inventory is pinned and before the first
search. A claim's latest entry is reusable when all of these hold:

- the claim text still hashes to the recorded `claim-hash`;
- the bibliography still resolves the citation to the same work (same DOI, or
  same archived file hash for DOI-less sources);
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
reused.

## Append, never rewrite

Entries are appended, and past entries are never edited or deleted: the
history of what was checked, when, and against which text is the point. When a
claim is re-verified, the new dated entry supersedes the old one simply by
being later. If parallel runs both appended and the file conflicts, keep both
sets of entries in date order.
