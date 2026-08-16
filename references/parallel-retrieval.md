# Parallel retrieval: one worker per independent source

Literature retrieval is usually the slowest part of a check, and sources are
independent once the inventory and ledger-reuse pass have pinned the work. On a
platform that supports subagents, fan the remaining work out rather than
fetching references serially.

## Coordination boundary

The coordinating agent alone performs the steps that require a consistent
global view:

1. inventory the complete requested scope;
2. resolve citation keys and group claims by source identity;
3. consult the ledger and remove reusable work;
4. dispatch citation-source workers and validate their results;
5. then dispatch novelty and uncited-claim searches with the cited-source
   evidence available; and
6. validate and merge worker results, append the ledger, and write the report.

Do not dispatch before the inventory and reuse decisions are complete. Search
results from a worker must never expand or shrink the pinned scope.

## Dispatch units

- **Citation verification:** launch one worker per distinct resolved source,
  giving it every in-scope claim attached to that source. A reference cited by
  five claims is one worker, not five duplicate downloads.
- **Bibliography audit:** launch one worker per non-reused bibliography entry.
- **Uncited claims and novelty:** launch one worker per independent claim or
  search question, but only after all citation-source results have returned and
  been validated. Give these workers the relevant cited-source evidence so the
  required citation-before-novelty order is preserved. These workers return
  leads, never novelty verdicts.
- **Version reconciliation:** launch one worker per non-reused baseline group
  (source identity, `version-read`, and archived hash).

Within each protocol stage, launch all independent units concurrently up to the
platform's safe worker limit; when there are more units than slots, keep the
slots full in waves. Never overlap citation-source retrieval with the later
novelty and gap-search stage. If the runtime has no subagent facility, or only
one unit remains, run the same units sequentially and state that execution mode
in the scope section. Never weaken retrieval or evidence requirements merely to
make a unit parallel.

## Worker contract

Give each worker the exact claims or entry fields it owns, the relevant
protocol and source-archive rules, any locator already resolved, and a unique
archive filename or staging path. A worker must return structured material for
the coordinator to review:

- unit identity and every assigned item;
- locators and registrar records queried;
- retrieval attempts and access failures;
- the proposed classification for each assigned item, with the evidence that
  determines it;
- update-relation findings;
- archive path, version read, access date, and SHA-256 when source text was
  read; and
- unresolved ambiguity or author input needed.

Workers may retrieve, read, hash, and archive their assigned sources. They must
not edit the manuscript or bibliography, append the verification ledger or its
JSONL companion, write the final report, or spawn further workers. The
coordinator checks that every dispatched item returned exactly once, that the
evidence supports the proposed classification, and that archive paths do not
collide before accepting a result.

## Failure and integrity handling

A failed or timed-out worker is not a skipped reference. On timeout, the
coordinator must cancel the worker and wait until termination is confirmed
before it retries, takes over, or accepts any result for that unit. If the
runtime cannot confirm termination, do not start a second writer for the same
archive path: quarantine the unit as incomplete and leave its staging path out
of the accepted archive. Retry a terminated unit once when useful, then
complete it in the coordinator if possible. Otherwise report
`unverifiable` for citation verification, `unconfirmed` for a bibliography
audit, or `detection incomplete` for a version sweep, with the actual failure.
For an uncited-claim investigation or novelty scan, record **search incomplete
— not run to completion**, naming the failed queries or channels; this is not a
zero-candidate or zero-lead finding and is never reusable. Do not let one failed
unit cancel successful independent work.

Only the coordinator appends fresh entries, in deterministic inventory order,
after all available worker results have been validated. This single-writer
rule prevents concurrent ledger appends, companion drift, duplicate entries,
and ambiguous supersession. Parallelism changes scheduling only: retrieved-not-
remembered, legal access, archive integrity, verdict standards, and
leads-not-verdicts remain unchanged.
