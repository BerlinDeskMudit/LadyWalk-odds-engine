# Architecture

> **This is a stub, and that is the honest state of the project.** Nothing here is a decision yet. This file records *which* questions are open, *what* each one blocks, and *what* the committed constraints already are. When an ADR lands, it moves from "open" to "decided" with a link.

## Current state

No implementation. 58 open questions tracked in [open design questions](../design/open-questions.md), one issue each. 35 are in **MVP**, 20 in **Portfolio Depth**, 3 in **Future Work**.

## The dependency spine

Almost everything hangs off three decisions. Doing them out of order produces work you will throw away.

```
                    ┌──────────────────────────────────────┐
                    │ Q15  Betting model                  │
                    │ fixed-odds │ pari-mutuel │ exchange  │
                    └───────────────┬──────────────────────┘
                                    │ determines liability, concurrency,
                                    │ capital, and regulatory surface
             ┌──────────────────────┼──────────────────────┐
             ▼                      ▼                      ▼
   ┌──────────────────┐   ┌──────────────────┐   ┌──────────────────┐
   │ Q16  where vig   │   │ Q17  curve       │   │ Q18  liability   │
   │      lives       │   │      choice      │   │      cap         │
   └────────┬─────────┘   └──────────────────┘   └────────┬─────────┘
            │                                              │
            └──────────────┬───────────────────────────────┘
                           ▼
              ┌────────────────────────────┐
              │ Q08  per-market            │
              │      serialization         │
              └─────────────┬──────────────┘
                            │ constrains
        ┌───────────────────┼───────────────────┐
        ▼                   ▼                   ▼
  ┌───────────┐      ┌────────────┐      ┌──────────────┐
  │ Q09 ACID  │      │ Q13 idem-  │      │ Q38 scale   │
  │    vs saga│      │    potency │      │    w/ order │
  └─────┬─────┘      └────────────┘      └──────────────┘
        │
        ▼
  ┌───────────┐      ┌────────────┐      ┌──────────────┐
  │ Q03 ledger│◄─────│ Q10 TOCTOU │      │ Q27 exactly- │
  │  (Q04 inv)│      │ Q45 neg-bal│      │     once     │
  └───────────┘      └────────────┘      └──────────────┘
```

Three more run across everything:

| Question | Cross-cutting because |
| --- | --- |
| **[Q36](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/36)** — monolith vs services | Decides the failure domain the money path lives in |
| **[Q35](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/35)** — kill switch | Must be able to stop all money movement regardless of shape |
| **[Q47](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/47)** — audit log | Every subsystem writes to it; retrofitting it is expensive |

## Open decisions

| # | Decision | Blocks | Issue |
| --: | --- | --- | --- |
| 1 | Betting model: fixed-odds / pari-mutuel / exchange | 8, 16, 17, 18, 38 | [#15](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/15) |
| 2 | Service boundaries and failure domains | 22, 36, 38, 39 | [#36](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/36) |
| 3 | Real-time transport | 22, 23, 24, 25 | [#21](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/21) |
| 4 | p99 budget for "bet accepted" | 37, 48 | [#37](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/37) |
| 5 | As-of reconstruction: event sourcing vs snapshot+delta | 2, 5, 6 | [#6](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/6) |
| 6 | Wallet locking strategy | 8, 10, 45 | [#11](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/11) |
| 7 | Odds update curve | 16, 17, 18 | [#17](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/17) |
| 8 | Fail safe vs fail open on pipeline death | 18, 31, 33 | [#31](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/31) |
| 9 | Clawback policy after a wrong settlement | 26, 27, 29 | [#29](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/29) |
| 10 | Wallet sharding (or an explicit decision not to) | 12, 38 | [#12](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/12) |
| 11 | Calculation versioning scheme | 5, 58 | [#58](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/58) |
| 12 | Webhook / fanout transport for 100K connections | 22, 25, 40 | [#22](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/22) |

## Committed constraints

These are *not* open. They follow from the question set and are not up for re-litigating without a new ADR.

### Money

- **Money is never a float.** A `Money` value type, `BIGINT` minor units + explicit currency in the database. Enforced by a CI lint gate, not by convention. ([Q01](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/1))
- **The ledger is the source of truth.** A `balance` column is a derived projection. There is no code path that mutates a posted entry. Corrections are compensating entries. ([Q03](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/3))
- **Double-entry sums to zero, with zero tolerance.** Not "within rounding". If a reconciliation finds a discrepancy, the run fails loudly. ([Q04](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/4), [Q30](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/30), [Q51](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/51))
- **Payouts are exactly-once and resumable.** Idempotency keys everywhere, and a settlement job checkpointed in the same transaction as the payout. ([Q13](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/13), [Q27](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/27), [Q28](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/28))
- **The server decides the price.** A client-supplied odds value is never trusted; execution requires a short-lived signed quote. ([Q19](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/19), [Q42](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/42))

### State

- **Odds history is immutable.** Every bet records the exact odds version it executed against, and every odds version records the calculation version that produced it. Any historical payout must be reproducible. ([Q05](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/5), [Q58](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/58))
- **Voids are compensations, not mutations.** A void never rewrites what was originally paid. ([Q07](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/7))
- **Every state change is audited tamper-evidently.** Hash-chained, append-only, not writable by the application role, retained past the dispute window. ([Q47](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/47))
- **Grading is hybrid with gated human override.** Overrides are the highest-risk admin action and are always attributable. ([Q26](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/26), [Q44](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/44))

### Failure behaviour

- **Fail safe, not fail open.** If the odds pipeline dies mid-event, betting on that market halts. Trading revenue against financial correctness is a choice, and this one is made explicitly. ([Q31](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/31))
- **One switch stops all money movement.** A platform-wide kill switch, audited, with defined trigger authority and a measured time-to-halt. ([Q35](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/35))
- **A bad market is quarantined automatically.** Plausibility invariants on odds; a breach blocks new bets and pages. ([Q33](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/33))
- **The ledger has its own DR posture.** RPO/RTO for the ledger are specified separately from application state, and the restore is drilled. ([Q34](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/34))

## Planned top-level shape

Provisional, subject to [Q36](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/36):

```
                    ┌──────────────────────────┐
   clients  ◄─────► │  bet-placement service   │  ◄── the money path
                    │  auth · rate limit · idem│      single ACID boundary (Q09)
                    └────────────┬─────────────┘
                                 │
              ┌──────────────────┼──────────────────┐
              ▼                  ▼                  ▼
      ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
      │  wallet +    │   │  odds        │   │  settlement  │
      │  ledger      │   │  engine      │   │  + grading   │
      └──────────────┘   └──────┬───────┘   └──────────────┘
                                │ odds updates
                                ▼
                     ┌──────────────────────┐
                     │  real-time gateway   │  stateless, thin
                     └──────────┬───────────┘
                                ▼
                              clients
```

Everything except the money path is a candidate to be a module in one deployable. The minimum defensible service split is wallet + bet placement, because that is the failure domain that can lose money.

## Documents

Written and maintained:

- [Documentation index](../README.md) — everything in `docs/`
- [open design questions](../design/open-questions.md) — the 58 questions and their issues
- [Roadmap](../roadmap.md) — the 260-issue build plan
- [Decision records](../adr/) — one file per decision; **none written yet**

Referenced but **not yet written**, because the question that unblocks each one is
still open. Listed as unwritten rather than stubbed, so a missing document is
visible in review instead of being mistaken for an empty one:

- `docs/schema.md` — ERD and the immutable/mutable column split (blocked on [Q02](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/2))
- `docs/slos.md` — SLIs, SLOs, error budgets (blocked on [Q48](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/48))
- `runbooks/` — cap breach, quarantined market, ledger imbalance, kill switch ([Q35](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/35), [Q51](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/51))
