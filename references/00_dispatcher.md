# Skim Test Outliner — Technical Operational Dispatcher

**Framework Author**: William Fitzpatrick (*Writer Science*)  
**Source Lecture**: [“Interference” Is Why You Can’t Write Well](https://www.youtube.com/watch?v=VrxufNaORhU)  
**Parent Collection**: [Master Collection](../../kirby-fitzpatrick-writers-collection/SKILL.md) | [Global Help](../../../kirby-help/SKILL.md)  

---

## 1. Cognitive Foundation: Interference Collapse and the Headline-as-Interface Model

Fitzpatrick's lecture names the disease before it names the cure. The disease is **interference**: the measurable friction produced when a text is ordered by the writer's *discovery* sequence instead of the reader's *decision* sequence.

An expert does not transcribe pre-formed thoughts; they use writing as the instrument that generates the thought. The resulting draft is therefore an honest trace of an untangling process — ideas pulled apart like a knotted string of Christmas lights, laid out in the order they happened to come loose. That ordering is correct for exactly one reader: the author. Fitzpatrick's central claim is that the pattern an expert uses to *think* about a subject actively fights the pattern a reader uses to *understand* it. That fight is the interference. It is not a discipline problem and it is not fixable by line editing, because line editing optimizes sentences inside a structure that is still scanning wrong.

In software engineering the collision is sharpest in architecture documents, because the reader is not a reader at all — they are a **decider under a time budget**. A senior engineer opening an RFC is running one algorithm:

```text
THE READER'S ALGORITHM (technical doc, senior engineer, no free time)
─────────────────────────────────────────────────────────────────────
STEP 1  Read the title.                       cost ~1 s
STEP 2  Scan HEADINGS ONLY. Stop at nothing.  cost ~8 s
STEP 3  Commit to reading a BODY.             cost 2–40 min
        ↑ conditional on STEP 2 succeeding
```

Step 3 is the only expensive step, and it is gated entirely by step 2. The body text is optional. The headings are mandatory. This is the whole lever: **headings are the only structural channel that a skimmer is guaranteed to traverse**, so any claim that lives only in body prose is behind a paywall whose toll is the full document.

```text
WRITER'S DISCOVERY ORDER (what the draft encodes)
    ┌──────────────────────────────────────────────────────────┐
    │ 1. found the reconciler dropping writes                  │
    │ 2. ruled out the scheduler      ← dead end, still in draft│
    │ 3. re-read the v2 ledger spec                            │
    │ 4. designed the dual-write window                        │
    │ 5. benchmarked p99 write latency                         │
    │ 6. argued with platform about the cutover date           │
    └──────────────────────────────────────────────────────────┘
                             │
                    INTERFERENCE  (the two orders disagree)
                             ▼
READER'S DECISION ORDER (what the reader needs, in this order)
    ┌──────────────────────────────────────────────────────────┐
    │ 1. What changes?        →  one claim                     │
    │ 2. What breaks?         →  one claim                     │
    │ 3. What is the risk     │  quantified boundary           │
    │    boundary?            │                                │
    │ 4. How do I undo it?    →  one claim                     │
    │ 5. What proves it?      →  one claim                     │
    └──────────────────────────────────────────────────────────┘
```

The **Skim-Test Outline** is the counter-measure. Instead of writing headings that *label a subject* ("Background", "Implementation", "Considerations"), you write headings that *assert the finding* — action-oriented, self-supporting, quantified sentences that carry the full technical claim with no body text required. The test is literal: cover the body, read the headings, and ask whether the change model is now complete. Fitzpatrick reaches the same instrument from the other direction through the **reverse outline** — extract the headings *after* drafting, then inspect the skeleton your prose actually built. That skeleton is the interference, made visible.

The mechanism is a **cost inversion**. A label heading pushes the decision cost onto the body, so the cost of learning anything is `O(body length)`. A claim heading pulls the decision cost into the skeleton, so the cost of learning everything important is `O(number of headings)`.

```text
ANTI-PATTERN — Label Headings  ("Skim Yields Nothing")
──────────────────────────────────────────────────────────
# Payments v3 Rollout
## Background
## Implementation
## Considerations
## Testing
## Rollback

SKIM RESULT (15 s):  "It's about payments v3, and there's a rollback plan."
INFORMATION EXTRACTED:  0 claims.  1 topic.  5 empty slots.
READER ACTION:          Read all 5 bodies to find out anything → 40 min.
HIDDEN COST:            Reviewer re-reads the doc twice, then posts
                        "can we huddle?" — the doc did not transmit.
```

```text
PATTERN — Claim Headings  ("Skim Yields the Whole Change")
──────────────────────────────────────────────────────────
# Payments v3: single-writer ledger, dual-write for 7 days, cut over on day 8
## Reconciler drops writes whenever the nightly job restarts mid-batch
## Router dual-writes to v2 + v3 for 7 days; ledger id is the idempotency key
## Dual-write costs +38 % p99 write latency and breaches SLO above 12k rps
## Cutover on day 8 is gated on all 340 contract fixtures passing on v3
## Rollback is one flag: LEDGER_PRIMARY=v2, ≤ 60 s dual-write replay, no loss

SKIM RESULT (15 s):  decision · mechanism · risk boundary · gate · undo.
INFORMATION EXTRACTED:  5 claims, each independently verifiable.
READER ACTION:          Open §3 only (the risk) → 90 s, then approve.
```

Treat the heading set as the document's **interface**, and the body as its **implementation**. A heading is a function signature: it declares what is true when you call it. Body prose is the function body: the mechanism, the dead ends, the caveats. Reading a signature is cheap and is enough to design against; reading a body is expensive and is only worth doing once a signature has earned it. Under this metaphor, the skim test is a **type check on the document**: if the headings do not compile into a coherent change model on their own, the document does not type-check, and no amount of body-polishing will fix the type error.

Three species of interference dominate engineering writing, and each one has its own heading-level signature:

1. **Discovery-order interference** — the doc narrates how the problem was found ("First I checked the scheduler, then…"). Signature: chronological headings, dead ends preserved, the answer in paragraph nine.
2. **Category-label interference** — a document skeleton inherited from a template rather than derived from claims. Signature: headings that are all nouns, all interchangeable between any two documents, and all predicted by the table of contents alone.
3. **Vocabulary-drift interference** — the heading names a concept the body never uses, or names it differently. Signature: heading says "write amplification", body says "IOPS overhead"; heading says "cache", body says "memoization layer". The skim-test reader cannot join the two, so the skim silently fails even when the claims are present.

Fitzpatrick's 15-second framing is an engineering-grade constraint, not a rhetorical flourish. At a realistic technical skim rate, 15 seconds buys roughly 5 to 8 heading lines. That yields the budget that governs every rule in §2:

```text
SKIM BUDGET ARITHMETIC
──────────────────────────────────────────────────────────────
15 s  ÷  ~2–3 s per heading line      ≈ 5–8 headings consumed
      ×  ≤ 12 words per heading        = 60–96 words of load-bearing text

∴ Every heading must carry a complete, standalone claim
  in twelve words or fewer.
  Anything longer is a paragraph wearing a heading's costume.
```

Sequencing note: claim headings are the *macro* layer, and they compose with the suite's paragraph-level instruments. The skeleton carries the claims; [Topic–Comment Elaboration](../../kirby-fitzpatrick-topic-comment-elaboration/SKILL.md) and [Cargo-Weighted Syntax](../../kirby-fitzpatrick-cargo-weighted-syntax/SKILL.md) carry the substance inside each body, and [Linear Relay Linking](../../kirby-fitzpatrick-linear-relay-linking/SKILL.md) stitches consecutive bodies so a reader who *does* commit to reading does not re-derive context at every heading boundary.

---

## 2. Core Transformation Protocols

### Protocol 1 — Reverse Outline First, Edit Never-First

Do not polish the draft you have. Extract it.

1. Copy every heading, in document order, into an empty file. If the draft has no headings, insert a heading at every paragraph boundary and then extract.
2. Read the extracted list **with the body inaccessible**. This is the skim test in its cheapest form.
3. Answer Fitzpatrick's five reader questions against the list alone: *what changes, what breaks, what is the risk boundary, how do I undo it, what proves it.*
4. Every question the list cannot answer is a **lying heading** or a **missing heading**. Record which.
5. Repair the skeleton, then re-point the bodies at the corrected headings. Line-editing before this step is wasted work on a structure that is about to change.

```text
DRAFT ──► extract headings ──► read skeleton alone ──► 5 questions
  ▲                                                          │
  └──────────── rewrite bodies to match repaired skeleton ◄──┘
                (line-edit LAST, never first)
```

### Protocol 2 — The Claim-in-Headline Grammar

Every heading conforms to one formula. Deviations are defects, not style choices.

```text
[VERB]  +  [OBJECT]  +  [QUANTIFIED CONSEQUENCE]  ( + [BOUNDARY or GATE] )

"Router   dual-writes   v2 + v3 for 7 days          (below 12k rps)"
"Cache    serves 30 s   of stale price      (breaks FIFO lot accounting)"
"Tests    pin 340       fixtures to v3 parity        (gated on CI green)"
```

Rules of the formula:

- **Verb first.** A heading is an action or an assertion about state change, never a topic. `Implementation details` is a topic; `Router writes to both ledgers` is a claim.
- **Consequence mandatory.** A heading with no measurable effect is a table-of-contents entry. If you cannot quantify it, mark it `Unquantified:` — the marker itself is information.
- **Boundary optional, never decorative.** Boundaries (`above 12k rps`, `after day 8`, `for EU tenants only`) are what makes a claim falsifiable. Include them exactly when a reviewer would otherwise ask "under what conditions?".
- **One clause.** Two verbs joined by `and` or `or` means two headings. Split.

### Protocol 3 — Self-Sufficiency (Screenshot Law)

A heading must survive being read in isolation: pasted into a Slack thread, a commit subject, a review comment, an email, or a screenshot of the document sidebar. Therefore, no heading may depend on context it does not carry.

Forbidden in headings: `it`, `this`, `that`, `the above`, `as discussed`, `the new approach`, `further considerations`. Also forbidden: any pronoun whose referent is in a previous heading or in the intro.

```text
✗  ## This doubles write cost                ← "this" = what?
✗  ## Further considerations                 ← further than what? about what?
✗  ## The new approach                       ← new relative to which?
✓  ## Dual-write doubles p99 write latency by 38 % for 7 days
```

### Protocol 4 — One Heading Level, One Abstraction Level (Headline Leveling)

All headings at the same depth must sit at the same level of abstraction and the same grammatical shape. A document whose `##` headings mix a mechanism, a risk, a gate, and a noun is a **Divergent Change** smell at the outline layer: unrelated reasons to read the same section, fused under one spine. Detect it by reading only the `##` lines as a list — if the list has no rhythm, the document has no spine.

```text
✗  ## Reconciler drops writes        (claim, mechanism)
   ## Considerations                 (label, no claim)
   ## We should cut over on day 8    (recommendation, different mood)
   ## The team                         (noun)
✓  ## Reconciler drops writes whenever the nightly job restarts mid-batch
   ## Router dual-writes to v2 + v3 for 7 days
   ## Dual-write breaches SLO above 12k rps
   ## Cutover is gated on 340 fixtures passing on v3
   ## Rollback is the flag LEDGER_PRIMARY=v2
```

### Protocol 5 — Token-Match the Body (No Synonym Drift)

Every load-bearing noun and verb in a heading must appear verbatim in its body. Vocabulary drift defeats the skim test silently: the reader accepts a heading, opens the body, and cannot locate the claim because it is phrased in different words.

1. Pick the heading's tokens once.
2. Reuse them in the body's topic sentence, unchanged, without elegant variation.
3. Ban cosmetic synonym substitution in the body: `dual-write` never becomes `mirrored persistence`; `contract fixture` never becomes `parity test`.
4. Run the suite's lexical passes as a second net: [Lexical Anti-Bloat Filter](../../kirby-fitzpatrick-lexical-anti-bloat-filter/SKILL.md) strips the substitutions, [Empty Verb Extractor](../../kirby-fitzpatrick-empty-verb-extractor/SKILL.md) removes the `is characterized by`-class hedges the drift hides behind.

### Protocol 6 — Mark Epistemic Status in the Headline

A committed-sounding heading over a guess is the most expensive defect in the whole instrument, because a skim reader *only* reads headings and therefore inherits the false confidence directly. Prefix the status when it is not `measured`.

| Prefix | Meaning | Body obligation |
|---|---|---|
| *(none)* | Measured or decided; defensible as stated | Show the number or the decision record |
| `Measured:` | Instrumented result, reproducible | Link the benchmark, name the environment |
| `Estimated:` | Modeled or extrapolated | State the model and its assumption |
| `Unverified:` | Belief, no evidence yet | State what would falsify it |
| `Blocking:` | Gate that stops the change | Name the owner and the unblock condition |

```text
✗  ## Dual-write costs 38 % more write latency          ← estimate sold as fact
✓  ## Estimated: dual-write adds ~38 % p99 latency (+/- 9 %) at 12k rps
✗  ## Contract tests will cover v3 parity
✓  ## Blocking: 340 contract fixtures must pass on v3 before cutover (owner: @ledger-team)
```

### Protocol 7 — Sequence Headings Must Serialize

When the document describes a multi-step change (migration, rollout, cutover, staged refactor), the headings must declare cardinality *and* be delivered in order — a broken contract otherwise. See [Theme Preview Roadmap](../../kirby-fitzpatrick-theme-preview-roadmap/SKILL.md) for the paragraph-level form and [Linear Relay Linking](../../kirby-fitzpatrick-linear-relay-linking/SKILL.md) for the inter-body stitch.

```text
## Migration runs in 3 stages: schema, dual-write, drain-and-cutover
## Stage 1 — Schema: add nullable tenant_id, no backfill, reversible in one DDL
## Stage 2 — Dual-write: router writes v2 + v3 for 7 days, ledger id is the key
## Stage 3 — Drain-and-cutover: LEDGER_PRIMARY=v3, v2 becomes read-only replica
```

### Protocol 8 — Timeboxed Skim Test, Then Repair

1. Hand the document's headings — nothing else — to a peer who does not know the change.
2. Start a 15-second timer. Read the headings at natural skim speed.
3. Ask them to restate: the change, the risk boundary, the rollback, and the evidence.
4. **Pass condition**: all four, unprompted, inside the budget.
5. Any failure is a heading defect. Repair the heading — not the body. Re-run. Only when the skeleton passes do the bodies earn their line edit.

### Conversion Table: Anti-Pattern → Clean Replacement

| Anti-pattern heading | Interference species | What the skim reader cannot do | Clean replacement |
|---|---|---|---|
| `## Background` | Category-label | Learn a single fact about the system | `## Reconciler silently drops writes on nightly-job restart` |
| `## Implementation` | Category-label | Know what was built or what changed | `## Router dual-writes to v2 + v3 behind the LEDGER_PRIMARY flag` |
| `## Considerations` | Category-label | Find the risk among mixed pros and cons | `## Dual-write breaches the 12k rps write SLO after 7 days` |
| `## Testing` | Category-label | Judge whether the change is actually proven | `## 340 contract fixtures pin v2/v3 parity; 12 gap cases are xfailed` |
| `## Rollback` | Category-label | Know the cost and the blast radius of undoing | `## Rollback is one flag flip: ≤ 60 s replay, no data loss` |
| `## Improving the caching layer` | Nominalization | Locate the verb, the delta, or the reason | `## 30 s TTL cache cuts DB reads 71 % but serves stale FIFO lots` |
| `## Why we should not use Kafka` | Rhetorical stance | Extract the technical constraint | `## Kafka adds a 4th stateful dependency for a 900 msg/s peak workload` |
| `## Introduction` | Category-label | Skip anything; the heading skims as required text | Delete it. Start with the strongest claim; the intro is dead weight |
| `## Other changes` | Dumping ground | Attribute any change to any reason | Split into one claim heading per change; if too small, delete it |
| `## This fixes the bug` | Anaphora + underclaim | Identify the bug, the cause, or the fix | `## Idempotency key on ledger writes ends duplicate postings` |

---

## 3. Engineering Application Scenarios

### Scenario A — Code Review Comments (Reviewer-Side)

A review thread is a document with the same interference problem, compressed into minutes. Comments that open with a category or an emotion ("Question here", "Not sure about this", "nit") force the author to parse the whole comment to learn whether they are blocked — and a multi-comment review with no claim spine cannot be triaged by the author at all. Apply Protocols 2, 3, and 6: every comment's first line is `[VERDICT] + [CLAIM] + [LOCUS]`, and the verdict prefix is part of the heading grammar.

```text
ANTI-PATTERN — Review thread that requires full reading to triage
──────────────────────────────────────────────────────────────────
Comment 1: "Hmm, have you thought about what happens when retry() fails
            repeatedly? Like if the downstream is down for a long time,
            wouldn't this keep hammering it? Not sure if that's intended."
Comment 2: "nit: naming"
Comment 3: "Also I'm a little worried about the migration ordering"

AUTHOR COST: read 3 bodies → classify 3 severities → guess 1 blocking set.
REVIEW LATENCY: +1 round trip, because comment 1's verdict is unknown.
```

```text
PATTERN — Claim-heading comments, triaged in one skim
──────────────────────────────────────────────────────────────────
1. 🔴 Blocking — retry() has no backoff ceiling; a 30-min downstream outage
   becomes 40k QPS of retry traffic. Cap at 5 attempts + jitter (L142).
2. 🟡 Non-blocking — ledger_id is built by string concat; collision risk at
   tenant_id > 2^53. Use the composite key type (L88).
3. ⚪ Nit — `getLedgerV2` should be `fetchLedgerV2` for suite naming parity (L19).

AUTHOR COST: read 3 headings → know the blocking set → fix 1, defer 1, accept 1.
REVIEW LATENCY: zero round trips. The verdict is in the skim layer.
```

The rule generalizes to reviewing *as* a skim test: before reading any body, read every comment's first line and the PR's file list. If that pass does not yield the set of blocking issues, the reviewer — not the author — has written an interference-heavy artifact.

### Scenario B — PR Descriptions (Author-Side)

The PR description is the highest-leverage skim-test surface in the whole engineering workflow, because it is read *thousands of times* by people who cannot afford to read it once: future git-archaeologists, release managers, on-call responders at 03:00 bisecting a regression. The title is a heading; the section headings are headings; nothing else is guaranteed to be read.

```text
ANTI-PATTERN — Description as clipboard dump
──────────────────────────────────────────────────────────────────
Title: "Fix ledger stuff"
## Summary
    Various fixes and improvements to the ledger service, mostly around
    the reconciliation flow. Also some cleanup and refactoring while I
    was in there. See commit messages for details.
## Test Plan
    Ran it locally, seems fine. CI is green.

GIT ARCHAEOLOGY COST: 8 months later, `git log --grep=ledger` returns this.
BISECT COST: unknown blast radius; the diff must be read in full.
```

```text
PATTERN — Description as skim-test outline
──────────────────────────────────────────────────────────────────
Title: Fix duplicate ledger postings on reconciler restart (idempotency
       key on write path)

## Reconciler re-posted all in-flight batches after a mid-batch restart
    Root cause: post_batch() had no idempotency key, so a restart replayed
    the batch into a fresh posting. Under prod volume this produced 1,204
    duplicate postings in the 2026-01-14 incident window.
## Fix: composite (tenant_id, batch_seq) idempotency key rejects replays
    post_batch() now writes the key inside the same transaction as the
    posting. Duplicate attempts return the original posting, not a new one.
## Blast radius: write path only; read path and schema are unchanged
    No migration. One new unique index on ledger_posting, added online.
## Measured: replay of the incident window produces 0 duplicate postings
    12,004-batch replay of 2026-01-14 data; index build added 41 s to
    deploy, 0 duplicates vs. 1,204 before. Repro in tests/replay_incident.py.
## Rollback is a revert; the new index stays but is inert
    Key check is behind IDEMPOTENT_POSTS (default on). Flag off = old path.
```

15-second reviewer path: title → `Blast radius` heading → `Rollback` heading → decision. The reviewer never needs the body unless the risk boundary applies to them. That is the entire design goal: **the skim layer is sufficient to approve; the body exists for the reader who must implement or challenge.**

### Scenario C — Architecture RFCs and ADRs

This is the target case named in the requirement: an architecture design doc in which a senior engineer understands the change in 15 seconds. An RFC/ADR has a *fixed* set of reader questions — Fitzpatrick's five, plus the decision record — and each canonical section maps to exactly one claim heading. The headings must be written as claims, so the ADR skeleton below is a **heading contract**, not a template of nouns.

```text
ANTI-PATTERN — ADR as template-filling  ("Status: Proposed", nothing else known)
────────────────────────────────────────────────────────────────────────────────
# ADR-0042: Ledger Storage Strategy
## Status        Proposed
## Context       Our ledger storage has various limitations.
## Decision      We will adopt a new storage strategy.
## Consequences  There are trade-offs to consider.
## Alternatives  We considered several other options.

SKIM RESULT (15 s): "There's a proposal about ledger storage. Unclear which one."
DECISION: blocked. Reviewer must read + ask. Classic interference artifact.
```

```text
PATTERN — ADR as skim-test outline  ("Read the headings, refute or approve")
────────────────────────────────────────────────────────────────────────────
# ADR-0042: Single-writer ledger on v3; v2 becomes a read-only replica on day 8
Status: Proposed (decided 2026-02-03)  ·  Owner: @ledger-team  ·  Supersedes: —  

## Status: Proposed — awaiting platform sign-off on the day-8 cutover window
    Not accepted yet. The single open question is the cutover date, not the design.

## Context: v2's dual writers race; 1,204 duplicate postings in 2026-01-14
    Two services write the ledger table. The race is structural, not a bug:
    both compute a balance from a stale read. Fixing it per-writer failed 3×
    (PRs #8811, #8840, #8902) because the race is in the shared table.

## Decision: one writer, one table — v3 ledger takes all writes, v2 read-only
    Ledger id is the idempotency key. One writer removes the race by
    construction rather than by locking; measured 0 duplicates in replay.

## Estimated: dual-write adds ~38 % p99 write latency for 7 days (+/- 9 %)
    Modeled from the 2026-01-20 shadow run at 11.8k rps. Above 12k rps the
    buffer queue is expected to saturate — that is the risk boundary.

## Consequences: reconciliation reads v2 for 7 days, so reports lag by ≤ 1 day
    Accepted. A second consequence: v2's schema is frozen during the window.

## Rejected: per-writer locking on v2 — deadlocks at 12k rps in the shadow run
    Rejected because the deadlock rate scaled with concurrency, not with data.

## Rollback: LEDGER_PRIMARY=v2 reverts writes in ≤ 60 s with no data loss
    v3 stays populated by dual-write, so a revert is a flag flip, not a migration.

## Blocking: 340 contract fixtures must pass on v3 before day-8 cutover
    Owner @ledger-team. Gate is recorded in CI as the `v3-parity` required check.
```

Senior-engineer 15-second pass: title (what changes) → `Context` (why now) → `Estimated` (the risk boundary) → `Rejected` (the alternative they were about to suggest) → `Rollback` (the safety) → verdict. The `Rejected` heading is the highest-value line in the document, because it pre-empts the reviewing engineer's most likely objection *in the skim layer*, converting a round trip into a read.

**Process integration.** The skim test is not advisory; it is the RFC's entry gate. A document may not enter review until its heading-only skeleton passes the Protocol 8 timebox with a reader who does not know the change. Where a reviewing persona is used to simulate the cold reader, run it against the headings *first* — a cold-reader auditor that only ever sees full prose will report sentence-level problems on a document whose spine is the actual defect ([Cold Reader PR Auditor](../../kirby-fitzpatrick-cold-reader-pr-auditor/SKILL.md)). And when the RFC must argue a proposal rather than record a decision, hold the claim skeleton intact and let [3-Part Proposal Engine](../../kirby-fitzpatrick-3part-proposal-engine/SKILL.md) carry the Status Quo → Instability → Resolution frame inside it.

---

## 4. Verification Checklist

- [ ] **Blind skim test passed.** A peer who does not know the change reads only the headings for 15 seconds under a timer, then restates all four of: the change, the risk boundary, the rollback, and the evidence. Any gap is repaired at the heading, not in the body — never by adding explanatory prose beneath a vague heading.
- [ ] **Zero label headings remain.** No heading in the document is a bare noun, a template name, or a category (`Background`, `Implementation`, `Considerations`, `Testing`, `Other`). Every heading contains a verb and a claim; count them and confirm the count equals the intended claim count.
- [ ] **Every heading satisfies the grammar and the budget.** Each conforms to `[VERB] + [OBJECT] + [QUANTIFIED CONSEQUENCE] (+ boundary)`, runs ≤ 12 words, carries one clause only, and survives the screenshot law in isolation (no `this`, `it`, `the above`, `as discussed`).
- [ ] **Non-measured claims are marked.** Every heading whose claim is not measured or decided carries its status prefix (`Estimated:`, `Unverified:`, `Blocking:`), and no estimate is presented as a measured fact.
- [ ] **Skeleton is coherent and token-matched.** Headings at the same depth share one abstraction level and grammatical shape (no Divergent Change in the spine); sequence headings declare and then fill their slots in order; and each heading's load-bearing tokens appear verbatim in its own body (zero synonym drift).
</｜｜DSML｜｜ parameter>
</｜｜DSML｜｜ invoke>
</｜｜DSML｜｜ calls>