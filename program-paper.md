# PaperRails — Research Program (autoresearch)

_The earning agent studies itself. Method borrowed from karpathy/autoresearch:
mutate one variable → run within a fixed budget → measure one metric → keep or
discard. Here the "experiment" is a strategy variant, the budget is the regular
30-minute GitHub Actions cadence, and the metric is **contracts won (escrow-locked)**
— the number that actually pays. This file is the program; `history.jsonl` is the
results ledger; the agent appends a machine-generated review on the first run of
each UTC month and rotates to the next hypothesis._

<!--STRATEGY-BLOCK
{ "hypothesis": "H1-full-price", "since": "2026-09-22T00:00:00.000Z", "reviewCount": 1 }
-->

## Metric (fixed, comparable across hypotheses)

- **Primary:** contracts won this calendar month (dealwork escrow-locked; `winsThisMonth` in the ledger).
- **Secondary (context, never the target):** bids placed, bid→contract conversion, profile/health state.
- One month per hypothesis minimum — bid acceptance is slow, so short windows measure noise.

## Hypothesis queue

| ID | Name | Knobs it changes | Status |
|---|---|---|---|
| H1-full-price | Bid the buyer's full budget | `priceMode: full` | **active since 2026-09-22** (baseline) |
| H2-undercut-10 | Bid 10% below budget | `priceMode: undercut` | queued |
| H3-high-volume | 3 bids/run instead of 2, $5 floor | `bidsPerRun: 3, minBudget: 5` | queued |

The agent rotates H1 → H2 → H3 → H1 … at each monthly review and rewrites the
STRATEGY-BLOCK above, so the very next run operates under the new knobs.

## What the agent may and may not mutate

- **May:** its own bid strategy (pricing mode, bid count, budget floor) and this paper's findings/rotation.
- **May not:** auth, wallets, payout settings, the security allowlist (`security-programs.json` is human-only), or anything in SECURITY-RESEARCH-POLICY.md. Strategy experiments never touch safety or money custody.

## Findings log

<!--FINDINGS-->
_(empty — first machine-generated review lands on the first run of the next UTC month)_

## Current honest baseline (as of 2026-09-22)

0 contracts won · 3 bids pending ($8/$10/$15) · profile complete · heartbeat 100%.
The first escrow-locked contract is the single most valuable data point this
program can produce, and every hypothesis is designed around reaching it.
