# citation-needed

A retrieval-grounded agent skill for verifying manuscript citations,
investigating uncited factual claims, and finding prior-work leads relevant to
novelty claims — every claim either shows a source that supports it or gets
flagged: [citation needed].

## Install

Copy or clone this repository into your agent's skills directory, for example:

```bash
git clone https://github.com/ipeirotis/citation-needed.git ~/.agents/skills/citation-needed
```

Then ask the agent to check specified citations or contribution claims. The
skill requires literature search and access to the actual source text.

## What it writes into your repo

Verification is cumulative: the skill leaves an auditable record in the
manuscript repository it runs in.

- `literature/sources/` — a copy of the exact text it read for every source —
  the full text when legally obtainable, otherwise the abstract or page
  snapshot that was reachable — named by citation key and DOI, with SHA-256
  hashes recorded. If the project
  configures a cloud store (a `LITERATURE_STORE` env var or a `store:`
  line naming a `gs://` or `s3://` prefix), copies go there instead.
- `literature/verifications.md` — a dated, append-only ledger of every
  verification: the claim, its source's DOI or URL, the archived copy's hash,
  the verdict, and the supporting passage. Later runs reuse these entries
  instead of re-fetching, and your repo's `AGENTS.md` gets a short pointer
  section so other agents find them.

Every source needs a publicly verifiable DOI or at minimum a stable URL. For
paywalled sources the skill asks you to supply the PDF (via your own access,
or print-to-PDF for web pages) rather than bypassing anything. Note that
committing paywalled PDFs to a public repo republishes them — the skill warns
and prefers a private bucket in that case.

## Safety model

The skill never verifies a source from memory, distinguishes inaccessible
sources from unsupported claims, treats novelty results as leads rather than
verdicts, and presents citation or wording changes only as proposals for the
author. It never edits the manuscript or its bibliography; the only files it
writes are the `literature/` artifacts and a pointer section in the repo's
agent-instructions file (`AGENTS.md`, or `CLAUDE.md` when that is what the
repo uses — and when the repo has neither, it proposes creating one rather
than writing it unasked), and it announces those writes in its report.

## Development

See `AGENTS.md` for repo conventions and `tasks.md` for the feature tracker.
