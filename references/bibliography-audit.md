# Bibliography metadata audit: does each reference identify a real work?

Use this pass when the user asks to audit, validate, or sanity-check a
manuscript's bibliography rather than the claims that cite it. It answers one
question per entry — does this reference identify a real, correctly described
work? — by checking the entry's fields against its DOI registrar's record. It
is deliberately cheap: no source text is fetched, read, or archived, so it
catches hallucinated or mangled references at a fraction of the cost of claim
verification, and it says nothing about whether any claim is supported — that
remains citation verification (`references/literature-checks.md`).

**Gate condition.** The same gate as every other capability: live retrieval.
The audit needs to fetch registrar metadata (`https://doi.org/…` resolution,
the registration agencies' APIs) and any URLs the entries carry; without that
access, do not fake the pass — report the missing access in
`Author decisions` and stop. Ledgered past audits remain reportable as dated
history even then.

## The protocol

Run the steps in order: the inventory is pinned before the first lookup, so
nothing a search returns can reshape which entries get audited.

### 1. Inventory the entries

List every bibliography entry in the requested scope before any lookup: parse
the `.bib` files the manuscript names (`\bibliography{}`,
`\addbibresource{}`), an inline `thebibliography` block, or the reference
list of a non-LaTeX manuscript. Record each entry's citation key, its claimed
fields (title, authors, venue, year, DOI, URL), and its `entry-hash`
(defined in `references/verification-ledger.md`). Do not drop an entry
because it looks hard to check: an unconfirmed entry is a reported outcome,
not a skipped one.

### 2. Consult the ledger

Mark every entry whose latest `bib:` ledger entry still reuses under the
ledger's rules (`references/verification-ledger.md`); only the rest go to
lookup. Reused verdicts are reported with their original dates, never as
fresh work.

### 3. Check each remaining entry

- **Entry has a DOI.** Resolve it and cross-check the registrar's record per
  the locator rules in `references/source-archive.md`, update-relations
  check included. The judgment is whole-record, with the tolerances defined
  there: an abbreviated venue, an online-first versus print year, or a small
  title typo with matching authors is still the intended work — flag the
  difference as a proposed correction, not a mismatch.
- **Entry has no DOI.** Search the registrars for the work (Crossref's
  bibliographic query on title, authors, and year; DataCite for datasets and
  reports). A record that confidently identifies the same work yields its
  DOI as a proposed addition — and, now that a DOI is in hand, the same
  update-relations screen the DOI-bearing branch runs: a retraction does
  not become invisible because the entry omitted its DOI. When the entry
  carries a URL instead, fetch it and check the page's own metadata against
  the entry; only an authoritative page — the publisher's, an institutional
  repository or preprint server, a standards body, official documentation —
  can confirm the work, because a personal or otherwise author-controlled
  page can assert whatever its author typed. A non-authoritative page
  corroborates but leaves the entry `unconfirmed`.
- Classify each entry as one of:
  - **confirmed**: a registrar record — or, for a DOI-less entry, its own
    authoritative URL — identifies the same work with materially matching
    fields and nothing to correct
  - **discrepant**: same work, but fields differ (wrong year, misspelled
    author, renamed venue, missing DOI); each difference becomes a proposed
    correction in `Author decisions`. A registrar match whose only gap is
    the entry's omitted DOI is discrepant, not confirmed — the discovered
    DOI stays a visible proposal until the author adopts it
  - **mismatched**: the DOI resolves to a different work, or the registrar
    authoritatively reports it unregistered (a DOI-not-found answer, not a
    transient error), or the registrar record contradicts the entry across
    the whole record; the reference is wrong as it stands and may be
    fabricated — report what the locator actually resolved to
  - **unconfirmed**: no DOI, no registrar record found, no reachable
    authoritative URL — or the lookup itself failed transiently (timeout,
    rate limit, registrar outage); record which in the entry. Registrar
    coverage is incomplete — books, theses, workshop papers, and older or
    non-English venues are routinely absent — and a transient failure is
    evidence of nothing, so `unconfirmed` is a question for the author or a
    retry on the next run, never a fabrication verdict on its own.

### 4. Update the ledger, then report

Append one dated `bib:` entry per audited reference (format and reuse rules
in `references/verification-ledger.md`) and confirm the writes landed before
returning the report. Then report:

- **Scope and lookups:** entries audited, entries reused from the ledger,
  registrars queried, and lookups that failed.
- **Bibliography audit:** one row per entry — key, verdict, the registrar
  record it was checked against, and each field-level discrepancy.
- **Author decisions:** proposed field corrections, proposed DOI additions,
  every `mismatched` entry with what its locator actually resolves to, every
  `unconfirmed` entry as a question, and any retraction or erratum flag the
  update check surfaced. Nothing is edited: the `.bib` file and the
  manuscript stay untouched, exactly as in every other capability.

A novelty-and-leads section is not part of this report; the audit makes no
claim about the literature beyond the identity of the entries it checked.

## Boundaries

- **Metadata is the evidence.** The verdict compares the entry against the
  registrar's record; the registrar fields that decided it are quoted in the
  ledger entry's `evidence:` line. No source text is fetched or archived,
  and `confirmed` means the reference identifies a real work — not that the
  work supports anything the manuscript says.
- **Fetched, not remembered.** Every verdict rests on a registrar record or
  page fetched this session, or on a ledgered dated audit — never on model
  memory of what a paper's title, venue, or year is.
- **Proposals only.** Corrections, DOI additions, and replacements are
  proposed in `Author decisions`; the audit never rewrites an entry, however
  obvious the typo.
