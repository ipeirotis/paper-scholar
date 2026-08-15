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
refresh-interval: 12 months       <!-- optional; DOI-backed re-fetch cadence in months, 12 when absent -->
```

One entry per verification, newest appended last, with these exact field
labels so entries stay greppable by humans and machines alike:

```markdown
## [2026-08-14T22:31Z] cite:smith2021 — introduction.tex:41
claim: "Smith et al. show that crowdsourced labels reach expert accuracy at one tenth the cost."
claim-hash: ab12cd34ef56
source: doi:10.1145/1234567.1234568
version-read: version of record
archived: literature/sources/smith2021--10.1145_1234567.1234568-4b1f22aa.pdf sha256:9f2a…
license: CC-BY-4.0
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
  over to the published version. When the text read is a preprint or another
  alternative version of a DOI-bearing work, record that version's own
  locator and revision beside the label — `version-read: preprint
  (arXiv:2106.01234v2)` — because the work-level DOI resolves to the version
  of record, not to the text actually read.
- `license:` records the redistribution authorization for the archived copy:
  the license name when one was found (`CC-BY-4.0`), `restricted` when the
  copy must not be redistributed and so lives in a private store, `unknown`
  when none was determinable (treated as restricted), or `n/a` when nothing
  was archived. This is what lets a later audit establish why a committed
  copy was permitted to be where it is.
- Novelty scans are logged too, since they age and their scope matters. Each
  lead carries the same source-identity, archive, and evidence fields as a
  citation entry — a lead a later run cannot reopen and re-read is not
  reusable evidence:

```markdown
## [2026-08-14T22:47Z] novelty — "we are the first to model X under Y"
searched: <the queries and databases used>
lead: doi:10.1234/abcd — Doe 2023, "Modeling X under Y-like constraints"
  version-read: version of record
  archived: literature/sources/doe2023--10.1234_abcd-7c09d1e2.pdf sha256:4e7b…
  license: unknown
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
license: n/a — nothing archived
verdict: unverifiable
evidence: none — nothing was retrieved; asked the author which work this key denotes
```

A zero-lead novelty scan likewise records `lead: none — searches returned
nothing on point` under its `searched:` line: the absence of a found overlap
is itself a dated finding, distinct from a scan that never ran.

The third capability — investigating an uncited factual claim — gets its own
entry form, since it is neither a `cite:` verification nor a novelty scan:

```markdown
## [2026-08-15T09:02Z] uncited — methods.tex:57
claim: "It is well known that crowd labels converge with enough redundancy."
claim-hash: c41f88a02b7d
candidate-source: doi:10.5555/wxyz — Roe 2018, "Redundancy and convergence in crowd labeling"
  version-read: version of record
  archived: literature/sources/roe2018--10.5555_wxyz-1a9e44b0.pdf sha256:8d1c…
  license: restricted — author-supplied copy, private store
  evidence: "convergence holds once redundancy exceeds…" (sec. 2)
status: candidate offered to the author; nothing inserted
```

An `uncited` entry reuses when the claim-hash matches, the recorded
candidate-source identity (its DOI or canonical URL) is unchanged, and the
archived copy validates — the bibliography-identity condition does not apply
while the claim remains uncited, since there is no citation to resolve. Once
the author adopts the candidate, the claim is a cited claim: later runs check
it under `cite:` with its new key, and the `uncited` entry stands as history.

When the search finds no suitable source, the entry records that outcome with
sentinels rather than a fabricated candidate:

```markdown
## [2026-08-15T10:05Z] uncited — methods.tex:57
claim: "It is well known that crowd labels converge with enough redundancy."
claim-hash: c41f88a02b7d
searched: <the queries and databases used>
candidate-source: none — searches returned no authoritative source
status: reported to the author; the claim currently has no supporting source
```

A no-candidate investigation ages like a novelty scan: reuse it only while
the claim text and the recorded `searched:` scope still match and the entry
is younger than about six months — the source that did not exist last year
may exist now.

Relocations get entries too, since past entries are never edited: when an
archived file moves (a fallback copy uploaded to the configured store once it
is reachable again), append a `relocation` entry, and let the reuse-time
archive check resolve a file through its newest relocation before comparing
hashes:

```markdown
## [2026-08-15T10:02Z] relocation — smith2021--10.1145_1234567.1234568-4b1f22aa.pdf
from: literature/sources/smith2021--10.1145_1234567.1234568-4b1f22aa.pdf
to: gs://bucket/prefix/smith2021--10.1145_1234567.1234568-4b1f22aa.pdf
sha256: 9f2a… (unchanged)
```

Successful refreshes are recorded the same append-only way: when a DOI-backed
file reaches its refresh interval, is re-fetched, and proves unchanged, append
a `refresh` entry so the refresh clock restarts — otherwise every later run
would re-download a source the cadence says to leave alone. The clock for a
file reads from its newest `refresh` entry, falling back to the original
verification's date:

```markdown
## [2026-08-15T10:20Z] refresh — smith2021--10.1145_1234567.1234568-4b1f22aa.pdf
checked: registrar updates none; re-fetched; sha256 unchanged (9f2a…)
```

## Reuse rules

Consult the ledger after the claim inventory is pinned and before the first
search. Supersession is per `(claim-hash, source identity)` pair: the newest
entry for that pair governs, so a sentence that cites two works keeps one
live entry per work, and a newer check of one source never hides the other's.
The governing entry is reusable when all of these hold:

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
when the author asks or when the claim or bibliography entry changed. A
mutable-URL snapshot gets one extra check before reuse: re-fetch the live page
and compare it with the archived snapshot where the evidence sits — unchanged
means the verdict reuses, changed means fresh verification, and an unreachable
page downgrades the entry to dated history ("verified against the page as of
<date>"), never presented as current. A DOI-identified version of record
skips the per-reuse re-fetch but is not assumed immutable: when reusing it,
query the registrar's metadata for updates (errata, corrigenda, retractions,
replacement versions — Crossref's update-to relations). An update means
re-fetch and re-verify, and any hash drift found on a re-fetch is handled per
`references/source-archive.md`. Declared updates are all the registrar can
show; silent replacements it cannot, so DOI-backed entries also get a
periodic content refresh: when the recorded fetch is older than the header's
`refresh-interval:` (in months; 12 when the field is absent), re-fetch the
file once and compare hashes — drift means a new dated copy and fresh
verification. Between refreshes the verdict still stands on the archived text
it was earned against; the refresh bounds how long a silent replacement can
go unnoticed.

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
claim is re-verified against the same source, the newer entry for that
claim-and-source pair supersedes the older by its heading timestamp — headings carry date and time (UTC) precisely so that same-day
entries cannot tie ambiguously. If parallel runs both appended and the file
conflicts, keep both sets of entries in timestamp order; should two entries
for the same claim still carry the same timestamp with different verdicts,
neither is reusable — report the conflict to the author instead of silently
picking one.
