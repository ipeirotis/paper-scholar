# Source archive: locators, legal full text, and storage

A verification is only auditable if the exact text that was read is preserved.
Links rot, publisher pages change, and preprints get revised; the archived copy
is what lets the author, a coauthor, or a later run re-open precisely what this
run read. Every source reported on therefore gets two things: a publicly
resolvable locator and an archived full-text copy in the project's source
store.

## Locator requirement

Every source needs a locator a stranger could resolve:

- **DOI, preferred.** Resolve it (follow `https://doi.org/<doi>`) and
  cross-check its metadata against the registrar that actually issued it:
  look up the registration agency first (`https://doi.org/ra/<doi>`) and
  query that agency's API — Crossref for most journal articles, DataCite for
  most datasets and reports, with mEDRA and others in the long tail — rather
  than assuming Crossref for everything. The registrar's metadata must
  identify the same work the bibliography entry describes — judged on the
  whole record, not field-by-field equality. An abbreviated venue, an
  online-first versus print year, or a small title typo with matching authors
  is still the intended work: verify the claim, and flag the bibliography
  discrepancy as a proposed correction in `Author decisions`. Only a DOI that
  resolves to a different work, or to nothing, marks the reference wrong or
  fabricated and keeps it out of the archive. A DOI registered outside
  Crossref is a valid DOI, not a suspect one.
- **Stable public URL, fallback.** For sources without a DOI (standards pages,
  arXiv abstracts, documentation, reports), record the canonical URL and the
  access date. Prefer the most durable form of the URL (an arXiv abs page over
  a mirror, a versioned document over a landing page).
- **Neither.** A reference that resolves to no DOI and no reachable URL is
  unverifiable. Ask the author what the citation points to; do not guess.

## Finding a legal full text

Search for an open, legitimate copy before involving the author, in this
order:

1. The publisher's own open-access link from the DOI resolution.
2. Unpaywall (`https://api.unpaywall.org/v2/<doi>?email=<contact>`), which
   returns the best legal open-access location and the license when one exists.
3. Preprint servers (arXiv, bioRxiv, SSRN) and the authors' own pages, checking
   that the copy found actually corresponds to the cited work. When only a
   preprint of a published paper is available, it may differ from the version
   of record: note which version was read in the ledger entry.

Never bypass a paywall, never present credentials, and never use pirate
mirrors. When only a paywalled copy exists, the request goes to the author in
`Author decisions`: ask them to download the PDF through their own access (or
print the page to PDF for web sources) and place it in the source store, naming
the exact path expected. Until the copy arrives, the claim is verifiable only
to whatever was legally reachable (usually the abstract) and is classified
accordingly. Whatever was legally read still gets archived: snapshot the
abstract or landing page exactly as a mutable web source (see the snapshot
rules below), append `--abstract` before the date-and-hash suffix, and record
it in the ledger with `version-read: abstract-only`, so even a partial
verification points at preserved text.

For non-paper web sources that are freely reachable, take the snapshot
directly: print the page to PDF with a headless browser when one is available,
otherwise save the fetched HTML or extracted text. Snapshots carry their access
date in the filename, since the live page can change.

## Choosing the store

Decide once per project, in this order, and record the choice in the ledger
header so later runs land in the same place:

1. **Explicit configuration.** A store declared by the project wins, and when
   declarations disagree the order is fixed: the `store:` line in the ledger
   header (`literature/verifications.md`) outranks the host repo's
   `AGENTS.md` literature section, which outranks a `LITERATURE_STORE`
   environment variable. The ledger header is where the existing archive
   already lives, and evidence continuity beats per-run convenience. Use the
   highest-ranked reachable declaration and report any conflict among them in
   `Author decisions` rather than resolving it silently. Each declaration
   names a `gs://bucket/prefix` or `s3://bucket/prefix`.
2. **Confirm access before trusting it.** Verify the tool and credentials
   actually work (`gcloud storage ls` / `gsutil ls` for GCS, `aws s3 ls` for
   S3) before uploading. If the configured store is unreachable, fall back to
   the repo folder and say so in the report rather than failing silently. A
   fallback is a per-run exception, never a new project choice: the
   configured store stays authoritative in the ledger header, the run's
   entries record the fallback paths actually used, and the report flags the
   stray copies so a later run with a working store can upload them and
   append a dated relocation note to their entries.
3. **Repo folder fallback.** With no configured bucket, archive into
   `literature/sources/` at the host repo root.

## Naming and integrity

- Name archived papers `<citekey>--<doi-slug>.pdf`. Build every filename
  component — citekey, DOI slug, title slug alike — by replacing each
  character outside `A–Z a–z 0–9 . _ -` with `_` (DOI slashes, colons, and
  angle brackets included), so names stay portable across filesystems,
  Windows included (for example `smith2021--10.1145_1234567.1234568.pdf`).
  Cited sources without a DOI use `<citekey>--<title-slug>` with the same
  sanitization, so every citekey case has one deterministic base name;
  sources with neither citekey nor DOI use the title slug alone. Snapshots of
  mutable pages append the access date and the first 8 hex characters of the
  file's own SHA-256 (`--2026-08-14-9f2a3c1d`), so two same-day fetches that
  differ can never share a name or overwrite each other.
- Compute the SHA-256 of every archived file and record it in the ledger entry.
  The hash is what lets a later run confirm it is looking at the same text: on
  re-download, compare hashes, and if they differ, save the new copy under the
  same date-and-hash suffix scheme as snapshots
  (`smith2021--10.1145_1234567.1234568--2026-08-15-4e7b21aa.pdf`) instead of
  overwriting — the original stays untouched at its recorded path so the text
  that was actually audited stays reopenable — and note the change in the
  ledger. A publisher silently replacing the PDF behind a DOI, or a revised
  preprint, is exactly the drift the hashes exist to catch.

## Copyright and repo hygiene

- Free to read is not free to redistribute. Record the license Unpaywall or
  the publisher reports, and commit a copy to a public repo only when that
  license explicitly permits redistribution (CC BY and kin). A paper that is
  merely readable on the publisher's site with no such license is treated
  like a paywalled one for storage purposes: private bucket, `.gitignore`d
  local folder, or quotes and metadata only.
- A paywalled or rights-restricted PDF committed to a **public** repository is
  republication. When the host repo is public and no private bucket is
  configured, warn the author and ask before committing such a file; the
  alternatives are a private bucket, a `.gitignore`d local `literature/sources/`
  (archived on the author's machine but not pushed), or archiving only the
  quoted passages and metadata.
- PDFs are binary and repos bloat: before committing any single file over
  ~10 MB, or once the archive folder crosses ~100 MB, raise Git LFS or a bucket
  with the author instead of pushing silently.
