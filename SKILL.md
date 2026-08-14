---
name: paper-scholar
description: Verify whether sources support claims in an academic manuscript, investigate uncited factual claims, or scan a stated contribution for overlapping prior work. Use when the task requires retrieving and reading actual papers or authoritative sources, producing claim-level evidence and novelty leads. Do not use for prose editing, citation formatting or BibTeX, number verification, data analysis, or unsupported citation judgments from memory.
---

# Paper Scholar

Check manuscript claims against sources retrieved and read during the task. Treat citation verification as an evidence audit and novelty scanning as a search for leads, not a definitive priority verdict. Never silently edit the manuscript or its bibliography.

## Select a capability

1. **Verify citations:** map each in-scope manuscript claim to its cited source and classify support.
2. **Investigate an uncited claim:** retrieve candidate authoritative sources without inserting a citation automatically.
3. **Scan novelty:** search for overlapping prior work and return candidates for the author to assess.

If the request does not identify the manuscript claims or search scope, ask one focused question before retrieval.

## Gate the work

Require a retrieval surface that can search for and fetch the actual source text. If a source cannot be accessed, classify it as unverifiable; never substitute memory, a search snippet, or another paper's characterization.

## Run the protocol

Read `references/literature-checks.md` before acting and follow its order: inventory claims, verify cited claims, then scan novelty or fill gaps. Record stable source links or identifiers and the passages that support each classification. Keep proposed citation or claim changes visibly separate from findings.

## Return

Return exactly:

1. **Scope and retrieval:** claims checked, search boundaries, sources fetched, and access failures.
2. **Citation audit:** one row per claim, classified as supported, partially supported, unsupported, or unverifiable, with evidence.
3. **Novelty and source leads:** candidate work, overlap, and why the author should inspect it; never claim exhaustive novelty.
4. **Author decisions:** flagged citation or wording candidates and unresolved questions. Nothing is edited automatically.
