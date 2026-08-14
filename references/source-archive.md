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
  cross-check its metadata against the Crossref API
  (`https://api.crossref.org/works/<doi>`) or DataCite for datasets. The
  resolved title, venue, and year must match the bibliography entry; a mismatch
  means the wrong DOI or a fabricated reference, and it goes to
  `Author decisions`, not into the archive.
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
accordingly.

For non-paper web sources that are freely reachable, take the snapshot
directly: print the page to PDF with a headless browser when one is available,
otherwise save the fetched HTML or extracted text. Snapshots carry their access
date in the filename, since the live page can change.

## Choosing the store

Decide once per project, in this order, and record the choice in the ledger
header so later runs land in the same place:

1. **Explicit configuration.** A store declared by the project wins: a
   `LITERATURE_STORE` environment variable, or a `store:` line in the host
   repo's `literature/verifications.md` header or its `AGENTS.md` literature
   section, naming a `gs://bucket/prefix` or `s3://bucket/prefix`.
2. **Confirm access before trusting it.** Verify the tool and credentials
   actually work (`gcloud storage ls` / `gsutil ls` for GCS, `aws s3 ls` for
   S3) before uploading. If the configured store is unreachable, fall back to
   the repo folder and say so in the report rather than failing silently.
3. **Repo folder fallback.** With no configured bucket, archive into
   `literature/sources/` at the host repo root.

## Naming and integrity

- Name archived papers `<citekey>--<doi-slug>.pdf`, where the DOI slug replaces
  `/` with `_` (for example `smith2021--10.1145_1234567.1234568.pdf`). Sources
  without a citekey use a short slug of the title; URL snapshots append the
  access date (`--2026-08-14`).
- Compute the SHA-256 of every archived file and record it in the ledger entry.
  The hash is what lets a later run confirm it is looking at the same text: on
  re-download, compare hashes, and if they differ keep both copies as separate
  dated files and note the change.

## Copyright and repo hygiene

- Open-access material (record the license Unpaywall or the publisher reports)
  can be archived anywhere, including a public repo.
- A paywalled or rights-restricted PDF committed to a **public** repository is
  republication. When the host repo is public and no private bucket is
  configured, warn the author and ask before committing such a file; the
  alternatives are a private bucket, a `.gitignore`d local `literature/sources/`
  (archived on the author's machine but not pushed), or archiving only the
  quoted passages and metadata.
- PDFs are binary and repos bloat: before committing any single file over
  ~10 MB, or once the archive folder crosses ~100 MB, raise Git LFS or a bucket
  with the author instead of pushing silently.
