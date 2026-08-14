# Literature checks: verifying citations and scanning novelty

Use this protocol when the user asks whether a cited work supports a manuscript
claim, whether a stated contribution overlaps prior work, or which source could
support an uncited factual claim.

**Gate condition.** This pass runs only when the environment grants literature
retrieval, a tool that fetches and reads the actual source text (web fetch and
web search, or an equivalent retrieval surface). When retrieval is missing, do
not fake the pass: say so, assert nothing about what any source contains, and
name the missing retrieval access in `Author decisions` and stop. Without retrieval, a citation check is memory, and a memory-cited source is fabrication.

This pass verifies citations and reports novelty leads; it never rewrites the
manuscript's claims on its own conclusion. Its two capabilities ship in one
order, citation verification before novelty scan, because a novelty claim can
only be judged against sources you have actually read.

## Where this lane sits under the master rule

The skill's master rule (never assert unverified substance) has a retrieval
branch: a citation is verified when you retrieved and read the source in this
session, with the passage that supports the use quoted. This lane is that
branch, and nothing else; the integrity norms below spell out what it excludes.

## The protocol

Run the steps in order. Do not reorder them: the claim inventory is pinned
before the first search, which is what keeps the search a verification instead
of a hunt for a convenient result.

### 1. Inventory the claims to check

Before any search or fetch, list every claim in the requested scope with its
location, split into two groups:

- **Cited claims**: each sentence that attributes a finding, method, or fact to
  a source ("X et al. show Y", "following the approach of Z"), with its citation
  key and the exact wording of what the manuscript says the source supports.
- **Contribution and novelty claims**: each sentence that asserts the paper is
  first, novel, or unlike prior work ("we are the first to", "no prior work
  addresses"), and each uncited "it is well known that" or "prior work has
  shown" claim that names no source.

Resolve each cited claim's key to a real work before you fetch anything: read
the manuscript's bibliography (the `.bib` files it names with `\bibliography{}`
or `\addbibresource{}`, an inline `thebibliography` block, or the reference
list of a non-LaTeX manuscript) so a bare `\cite{key}` maps to a title, venue,
year, and DOI. A key you cannot resolve to a specific work is unverifiable; do
not guess which paper a bare key denotes or search by the key string, since
that risks checking the wrong source.

The inventory must be complete before you fetch a single source, so what you
read can never reshape which claims get checked. Do not add claims to the list
because a search surfaced them, and do not drop a claim because verifying it
looks hard: an unverifiable claim is a reported outcome, not a skipped one.

### 2. Verify each citation

For every cited claim, retrieve and read the actual source (fetch the paper, its
abstract, or the specific section that would carry the claim). Judge whether the
source supports the sentence as written, and classify each as one of:

- **supported**: the source states what the manuscript attributes to it; quote
  the passage that supports it
- **unsupported**: the passage you read states something weaker than, or
  contrary to, the claim; quote what it does say and describe the gap. Reserve
  this for when you reached the text that would carry the claim: a claim missing
  from an abstract you could not read past is unverifiable, not unsupported,
  since most method and result detail lives in the body, and an absence there is
  no evidence against the claim.
- **unverifiable**: the source could not be retrieved (paywalled, not found,
  ambiguous or unresolvable key), or the passage that would settle the claim was
  not reachable (for example only the abstract was available and it neither
  states nor contradicts the claim); say why

### 3. Scan novelty and fill gaps

For each contribution or novelty claim, search for prior or overlapping work and
report leads, not verdicts. A lead names the candidate work and points the
author at what to read ("Doe (2023) appears to address the same problem in a
different setting; read sections 3-4"); it never concludes that the paper is or
is not novel. For an uncited "well known" claim, search for the source that
would support it and offer it as a candidate citation with the passage attached.
When a search returns nothing on point, say so: absence of a found overlap is a
lead too, and it is not proof the claim is novel.

### 4. Report

Return the four sections described below. Mark proposed citation additions and
recalibrated claims as candidates, attach their retrieved sources, and leave the
adoption decision to the author.

## Integrity norms

- **Retrieved, not remembered.** Every source you report on was fetched and read
  in this session, with title, venue, year, and the specific passage that bears
  on the use. A citation from model memory is treated as fabricated, and a
  novelty judgment from model knowledge is not a finding.
- **Leads, not verdicts.** A novelty scan returns candidates for the author to
  judge ("X (2023) appears to do Y; read sections 3-4"), never a ruling on the
  paper's contribution. Never rewrite the manuscript's novelty claim on your own
  conclusion: propose the recalibrated claim and cite the evidence, and let the
  author decide whether the overlap is real.
- **Additions are flagged.** A new or changed citation enters the text only as a
  proposed edit with the retrieved source attached, honoring the existing rule
  that citation changes are the author's decision. Never silently insert a key or
  alter an existing one.
- **A recalibrated claim changes the paper.** When the author accepts a novelty
  correction or a new citation, the same claim may echo in the abstract,
  introduction, and discussion: recommend a whole-paper consistency check after the corrections land, and say so in the report.

## Reporting conventions

- **Scope and retrieval:** name the claims checked, search boundaries, sources fetched, and access failures.
- **Citation audit:** give one row per claim, grouped as supported, partially supported, unsupported, or unverifiable. Name the manuscript location and attach the retrieved evidence.
- **Novelty and source leads:** name each candidate work, the apparent overlap, and what the author should read. A lead is never a novelty verdict.
- **Author decisions:** ask one question per unsupported or unverifiable citation, candidate citation, proposed wording change, and novelty lead. The author decides what enters the manuscript.
