# LadyWalk Odds Engine

**An open-source betting platform, built in public, designed to survive the questions a principal engineer asks before signing off.**

> **Status: design phase.** This repository currently contains the *question set* and the *tracker*, not an implementation. That is deliberate. The hard part of a betting platform is not writing a bet-placement endpoint — it is the 58 design questions in [`QA.md`](QA.md), each one a trap that has sunk a real production system. They are tracked as GitHub issues, and each will be closed with a committed artefact, not a paragraph.

[![Questions tracked](https://img.shields.io/badge/questions-58-informational)](QA.md)
[![MVP](https://img.shields.io/badge/milestone-MVP-0%2F35-blue)](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/milestone/1)
[![Portfolio Depth](https://img.shields.io/badge/milestone-Portfolio-0%2F20-blue)](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/milestone/2)
[![Future Work](https://img.shields.io/badge/milestone-future-0%2F3-lightgrey)](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/milestone/3)
[![License: MIT](https://img.shields.io/badge/license-MIT-green)](LICENSE)

---

## Why this repo exists

Most betting-platform projects are a `POST /bets` endpoint and a `Decimal` field that quietly becomes a `float`. The interesting engineering is everything around it:

- **Money correctness.** Integer minor units, an append-only double-entry ledger, a zero-tolerance sum-to-zero invariant, and reversal-by-compensation instead of mutation.
- **Concurrency under load.** What serialises 10,000 bet placements on one market inside 50ms — and how that choice constrains horizontal scaling.
- **Odds you can prove.** An immutable odds history so that any historical payout can be reproduced byte-for-byte, with the calculation version that produced it.
- **Settlement that is exactly-once.** A job that can crash at 70% and resume without double-paying anyone or skipping anyone.
- **Adversarial clients.** The client must never be able to assert its own price. A bot is faster and more patient than you are.

The point of tracking the questions publicly is that **"we thought about it" is not the same as "we measured it"**. Every issue carries acceptance criteria that require an artefact — an ADR, a benchmark, a test that fails against the pre-fix code.

## Start here

| | |
| --- | --- |
| [`QA.md`](QA.md) | The 58 questions, grouped by subsystem, each linked to its issue |
| [All 58 issues](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues) | The actual work |
| [`CONTRIBUTING.md`](CONTRIBUTING.md) | How to claim an issue, what "done" means, ADR conventions |
| [`ARCHITECTURE.md`](ARCHITECTURE.md) | Which decisions are still open, and which issues will close them |
| [`SECURITY.md`](SECURITY.md) | Reporting a vulnerability in the money path |

## The questions at a glance

58 questions across 11 subsystems. The type is derived from the wording — a question about something that *could be wrong* is a `bug`, a question about something that *must be built* is a `feat`, a question that *cannot be answered without measuring* is a `spike`.

| Subsystem | Q | Covers |
| --- | --: | --- |
| Data Modeling & Correctness | 7 | money representation, schema, ledger, odds versioning, voids |
| Concurrency & Consistency | 7 | serialization, ACID boundaries, TOCTOU, locking, idempotency |
| Odds Engine | 6 | fixed-odds vs pari-mutuel vs exchange, vig, curves, liability, slippage |
| Real-Time Delivery | 5 | transport, fanout at 100K, resync, backpressure, hot markets |
| Settlement & Grading | 5 | grading model, exactly-once payout, resumability, clawback, reconciliation |
| Failure Modes & Reliability | 5 | fail safe vs fail open, circuit breakers, quarantine, DR, kill switch |
| Scalability & Architecture | 6 | service boundaries, latency budget, read/write split, hot partitions, rate limits |
| Security | 6 | price spoofing, bots, admin auth, negative balance, replay, audit log |
| Observability | 4 | SLIs/SLOs, silent-bug detection, liability dashboards, ledger alerts |
| Testing | 4 | load, property-based fuzzing, crash injection, chaos |
| Deployment | 3 | zero-downtime, production-like staging, calc versioning |

**Type split:** 39 `feat` · 15 `task` · 2 `bug` · 2 `spike`

The two `bug` issues are the ones that would have been shipped by accident:

- **[Q10](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/10) — TOCTOU double-spend.** A balance check and a balance deduction that are not one atomic step. Reachable by any user with two concurrent requests, and completely invisible in manual QA.
- **[Q45](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/45) — negative-balance exploit.** Validate-then-deduct is not atomic, so two requests both pass validation and both deduct.

## Issue scheme

**Milestones encode phase, not urgency:**

| Milestone | Count | Meaning |
| --- | --: | --- |
| **MVP** | 35 | Needed before the platform can honestly be called shippable |
| **Portfolio Depth** | 20 | Separates a working demo from a defensible design |
| **Future Work** | 3 | Not needed yet — document the design and the trigger that would make it real |

**Labels:** `type:` (`feat` `bug` `task` `spike`) · `area:` (11 subsystems) · `phase:` · `priority:` (`critical` `high` `medium` `low`).

Every question is ranked `priority:critical` if getting it wrong loses money or breaks a regulatory guarantee. 16 of the 58 are critical.

## Planned shape

Not decided yet — the issues are the reason. What is already committed:

- **Money is never a float.** A `Money` value type, `BIGINT` minor units in the database, and a CI gate that fails the build on a float anywhere in a money path. ([Q01](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/1))
- **The ledger is the source of truth, not the balance.** `balance` is a derived projection. Every correction is a compensating entry. ([Q03](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/3))
- **The server decides the price.** A short-lived, signed quote token. A client-supplied odds value is never trusted. ([Q19](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/19), [Q42](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/42))
- **Every state change is audited, tamper-evidently.** Hash-chained, append-only, not writable by the application role. ([Q47](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/47))
- **One switch stops all money movement.** A platform-wide kill switch that halts bet acceptance within seconds, with audited trigger authority. ([Q35](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/35))

The three decisions everything else hangs off, in order:

1. **[Q15](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/15)** — fixed-odds, pari-mutuel, or exchange? These have completely different concurrency and liability models. Every other subsystem is downstream of this one.
2. **[Q08](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/8)** — what serialises concurrent bets on one market?
3. **[Q36](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/36)** — are the wallet and bet placement separate failure domains from odds and market data?

## Scope and safety

This is an educational, open-source project. It is **not** a licensed gambling operator, publishes no odds, takes no bets, and handles no real money. All fixtures are synthetic.

Legal and regulatory requirements (licensing, age verification, KYC, responsible-gambling tooling, geo-restriction, tax reporting) are treated as **cited research in an ADR, not legal advice**, and are tracked as such in [Q29](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/29) and [Q15](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/15).

## Contributing

Claim an issue, satisfy its acceptance criteria, close it with the artefact linked. See [`CONTRIBUTING.md`](CONTRIBUTING.md).

## License

[MIT](LICENSE)
