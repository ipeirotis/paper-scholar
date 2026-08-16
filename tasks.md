# Tasks

Feature tracker for the citation-needed skill. Agents: read this before working
on the repo. New requests go under **Backlog** with the date they were filed;
when work lands, move the item to **Done** with the landing date and the files
that implement it. Items under **Proposed** are ideas awaiting the author's
approval — do not implement them unprompted. Items the author has decided
against move to **Decided, no action** with the decision recorded — do not
reopen them without a new request.

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

- **2026-08-15 — Rename the skill to citation-needed.** Final name in the
  paper-scholar → chapter-and-verse → citation-needed sequence, superseding
  the chapter-and-verse rename above: the author judged that name hard to
  remember, and "[citation needed]" is the Wikipedia tag every academic
  already knows — resolving it is literally the skill's job. The GitHub repo is
  `ipeirotis/citation-needed` (old URLs redirect). `LITERATURE_STORE` and all
  host-repo artifact paths were already rename-proof; host ledgers written
  under earlier names stay valid, since `maintained-by:` is informational.
  (`SKILL.md`, `README.md`, `AGENTS.md`, `tasks.md`, `agents/openai.yaml`,
  `references/verification-ledger.md`)

- **2026-08-15 — Global consistency pass after the slim-out and renames.**
  Aligned the verdict taxonomy — `literature-checks.md` step 3 now defines
  all four verdicts including *partially supported*, which SKILL.md, the
  reporting conventions, the ledger examples, and the reuse rules already
  used — and replaced the leftover blue-pencil framing ("this lane", "the
  master rule") with standalone wording.
  (`references/literature-checks.md`)

- **2026-08-15 — Retraction and erratum check at verification time.** Fresh
  DOI-backed verifications now check the registrar's update relations —
  retractions, errata, corrigenda, expressions of concern; Crossref's update
  metadata incorporates the Retraction Watch database — at DOI resolution,
  flag the update with the verdict in the citation audit and `Author
  decisions`, and record it in the entry's `updates:` field. The reuse-side
  slice had already shipped with the ledger (2026-08-15): reusing a
  DOI-backed entry re-queries the registrar before the old verdict stands.
  (`references/source-archive.md`, `references/literature-checks.md`,
  `references/verification-ledger.md`)

- **2026-08-15 — Bibliography metadata audit.** Fourth capability, approved
  by the author: a cheap standalone pass confirming each bibliography entry
  identifies a real work — its DOI resolved and compared whole-record against
  the registrar per the source-archive tolerance rules, DOI-less entries
  matched by registrar search or their own URL, update relations screened
  along the way. Verdicts confirmed / discrepant / mismatched / unconfirmed
  (registrar absence is a question for the author, never a fabrication
  verdict on its own); results ledgered as `bib:` entries keyed by
  entry-hash; corrections and DOI additions only ever proposed. The trigger
  description now excludes "citation style formatting" rather than all
  BibTeX work. (`references/bibliography-audit.md`, `SKILL.md`,
  `references/verification-ledger.md`, `AGENTS.md`, `README.md`)

- **2026-08-16 — Version-of-record reconciliation (proactive half).** Fifth
  capability, approved by the author: a ledger sweep that detects when a
  source read as a preprint or partial text has a published or fuller
  version — registrar preprint relations, arXiv journal-ref, bibliographic
  search, with the update-relations screen along the way — archives the new
  text when legally reachable, diffs it against the archived read at section
  and evidence-passage granularity, and proposes re-checks for claims whose
  evidence changed; intact claims keep their dated verdicts, and no verdict
  ever changes in a sweep. Results ledgered as dated `vor` entries keyed by
  source identity (none-found results stand ~6 months); verification runs
  use the newest `vor` entry as a bridge when the version predicate blocks
  reuse, and offer the sweep for out-of-scope preprint reads instead of
  pulling them in. The reuse-time slice had already shipped in the ledger
  rules (2026-08-14). (`references/version-reconciliation.md`,
  `references/verification-ledger.md`, `references/literature-checks.md`,
  `SKILL.md`, `README.md`, `AGENTS.md`, `agents/openai.yaml`)

- **2026-08-16 — Machine-readable ledger companion.** Approved by the
  author: an opt-in `companion:` line in the ledger header keeps a derived
  JSONL beside `verifications.md` — one JSON object per entry, `ts`, `type`,
  and `heading` plus keys copied verbatim from the Markdown field labels,
  appended in the same write as every Markdown append and backfilled when
  first enabled. The Markdown stays the record: runs never read the
  companion, and on any disagreement the JSONL is regenerated from the
  Markdown, never the reverse. Without the header line nothing changes —
  the stable field labels keep the Markdown greppable, as before.
  (`references/verification-ledger.md`, `SKILL.md`, `README.md`,
  `AGENTS.md`)

## Proposed (awaiting approval)
- **Eval suite.** Test manuscripts with known-good, known-bad, and
  unverifiable citations to benchmark the skill's classifications and catch
  regressions (skill-creator eval loop), plus trigger-description
  optimization once behavior settles.

## Backlog

(empty — new requests go here with the date they were filed)

## Decided, no action

- **2026-08-15 — No blue-pencil sync.** The author decided the behaviors
  landed here (source archive, locator rules, verification ledger, reuse
  rules, retraction screen, bibliography audit) stay standalone: blue-pencil
  is not updated, nothing is upstreamed or downstreamed, and this repo's
  protocol evolves independently from blue-pencil's scholar lane. Divergence
  between the two is accepted, not tracked — the standing sync note in
  `AGENTS.md` was retired accordingly. Closes the 2026-08-15 backlog item.
- **2026-08-15 — Facts-and-figures extraction dropped.** The author decided
  the planned carve-out of blue-pencil's analyst lane into
  `ipeirotis/facts-and-figures` is not needed; the backlog item is closed
  without action. If the idea is revived, it will be tracked in its own
  repo, not here.
