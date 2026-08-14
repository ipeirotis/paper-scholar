---
name: chapter-and-verse
description: Verify whether sources support claims in an academic manuscript, investigate uncited factual claims, or scan a stated contribution for overlapping prior work. Use when the task requires retrieving and reading actual papers or authoritative sources, producing claim-level evidence and novelty leads. Maintains the host repo's literature archive (a full-text copy of every source read, in a configured cloud bucket or a repo folder) and a dated verification ledger so later runs reuse past results instead of re-fetching. Do not use for prose editing, citation formatting or BibTeX, number verification, data analysis, or unsupported citation judgments from memory.
---

# Chapter and Verse

Check manuscript claims against sources retrieved and read during the task. Treat citation verification as an evidence audit and novelty scanning as a search for leads, not a definitive priority verdict. Never silently edit the manuscript or its bibliography.

## Select a capability

1. **Verify citations:** map each in-scope manuscript claim to its cited source and classify support.
2. **Investigate an uncited claim:** retrieve candidate authoritative sources without inserting a citation automatically.
3. **Scan novelty:** search for overlapping prior work and return candidates for the author to assess.

If the request does not identify the manuscript claims or search scope, ask one focused question before retrieval.

## Gate the work

Require a retrieval surface that can search for and fetch the actual source text. If a source cannot be accessed, classify it as unverifiable; never substitute memory, a search snippet, or another paper's characterization. Ledger entries are the one exception: a past verification recorded in the host repo's ledger is dated evidence, not memory, and may be reported even when retrieval is unavailable — always with its date, never as fresh work.

## Reuse before you retrieve

Before fetching anything, read the host repo's verification ledger (`literature/verifications.md`) if it exists; `references/verification-ledger.md` defines the format and the reuse rules. A claim already verified against the same source is not re-fetched: report the prior result with its date. This is what keeps repeated runs cheap and keeps the audit trail continuous across sessions.

## Run the protocol

Read `references/literature-checks.md` before acting and follow its order: inventory claims, consult the ledger, verify cited claims, then scan novelty or fill gaps. Every source read gets a resolvable locator (a DOI, or failing that a stable public URL) and a full-text copy archived per `references/source-archive.md` — a cloud bucket when the project has one configured, otherwise the host repo's `literature/sources/` folder. Paywalled sources are requested from the author, never bypassed. Keep proposed citation or claim changes visibly separate from findings.

## Return

Return exactly:

1. **Scope and retrieval:** claims checked (marking which were reused from the ledger), search boundaries, sources fetched and archived, and access failures.
2. **Citation audit:** one row per claim, classified as supported, partially supported, unsupported, or unverifiable, with evidence; reused rows carry their original verification date.
3. **Novelty and source leads:** candidate work, overlap, and why the author should inspect it; never claim exhaustive novelty.
4. **Author decisions:** flagged citation or wording candidates, requests for paywalled PDFs the author must supply, and unresolved questions. Nothing is edited automatically.

After reporting, append the fresh results to the host repo's ledger as dated entries and say what was written (`references/verification-ledger.md`).
