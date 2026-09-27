# QA — L8-Level Technical Question Set

> The kind of questions a staff/principal engineer would force you to answer before signing off on the design. Organized by subsystem, 58 questions.

**Status legend:** `[ ]` open · `[~]` in progress · `[x]` answered

---

## Data Modeling & Correctness

1. [ ] Do I store money as integers (cents) or fixed-point decimals — and have I banned floats everywhere, including in the ORM layer?
2. [ ] What's my canonical schema for `market → outcome → bet → settlement` — and is it normalized enough to avoid update anomalies during grading?
3. [ ] Am I using a single mutable `balance` column, or a full immutable, append-only ledger (debits/credits) that balance is *derived* from?
4. [ ] If ledger-based: how do I guarantee the ledger always sums to zero across the system (double-entry invariant), and how do I test that invariant continuously?
5. [ ] How do I version odds over time — do I keep an immutable odds history table so I can prove what odds a user actually got, forever?
6. [ ] What's my strategy for storing "as of" state — can I reconstruct exactly what any market looked like at any past timestamp (event sourcing vs snapshot + delta)?
7. [ ] How do I model void/push/partial-void outcomes without corrupting historical settlement records?

## Concurrency & Consistency

8. [ ] When 10,000 users hit "place bet" on the same market within 50ms of each other, what serializes them — DB row locks, optimistic concurrency with retry, or a single-writer queue per market?
9. [ ] Is bet placement + balance deduction wrapped in a single ACID transaction, or is it two-phase across services — and if the latter, what's my compensation/saga logic on partial failure?
10. [ ] How do I prevent a double-spend where a user's balance check and balance deduction race across two concurrent requests (classic TOCTOU)?
11. [ ] Am I using SELECT FOR UPDATE, advisory locks, or optimistic versioning (version column + compare-and-swap) on the wallet row — and what's the throughput ceiling of each?
12. [ ] If I shard the wallet table, how do I keep balance-deduction transactions atomic across shard boundaries (or do I ensure a user's wallet is always single-shard)?
13. [ ] What's my idempotency key design so a client retry (network blip) never double-places a bet or double-pays a settlement?
14. [ ] How do I handle the "last odds/last slot" race — first-writer-wins, queue-based FIFO, or do I reject and let the client re-quote?

## Odds Engine

15. [ ] Is this fixed-odds, pari-mutuel pool, or exchange-style (peer matching) — and do I understand that these three have completely different concurrency and liability models?
16. [ ] Where does the vig/margin get baked in — at market creation, or dynamically as liability shifts?
17. [ ] If odds move based on bet volume, what's the update function — linear, logistic, or a proper market-maker curve (e.g., something LMSR-like)?
18. [ ] How do I cap my platform's liability on any single outcome, and what happens when a cap is hit mid-bet (reject, requeue, auto-hedge)?
19. [ ] Do I snapshot the odds a user sees vs. the odds at execution time — and what's my tolerance/slippage policy if they differ?
20. [ ] How do stale odds get invalidated across all connected clients within an acceptable latency budget?

## Real-Time Delivery

21. [ ] WebSockets, SSE, or long-polling — and can I justify the choice against reconnection semantics, not just "WebSockets are cool"?
22. [ ] How do I fan out odds updates to potentially 100K+ concurrent connections without every app server holding a giant socket map — Redis pub/sub, Kafka + a thin gateway layer, or a dedicated real-time service?
23. [ ] On reconnect, how does a client resync to current state without replaying every event since disconnect (snapshot + delta vs full replay)?
24. [ ] How do I handle backpressure when a slow client can't keep up with odds update velocity during a live in-play event?
25. [ ] What's my fanout architecture if one market suddenly needs to push to 50K sockets simultaneously (a "hot market" thundering herd)?

## Settlement & Grading

26. [ ] Is grading manual, automated via a results feed, or hybrid with human override — and what's the audit trail on overrides?
27. [ ] What's my exactly-once guarantee on payout execution — how do I ensure a settlement job that crashes mid-run doesn't double-pay or skip users?
28. [ ] How do I make settlement idempotent and resumable (checkpointing which bets have been paid within a batch)?
29. [ ] What happens if a results feed reports a wrong outcome and I've already paid out — do I have a reversal/clawback mechanism, and is it even legal/expected to claw back?
30. [ ] How do I reconcile "total paid out" against "total staked minus margin" as a hard invariant check after every settlement run?

## Failure Modes & Reliability

31. [ ] If my odds-update pipeline dies mid-event, does the system fail safe (halt betting on that market) or fail open (accept bets on stale odds)? Which have I chosen and why?
32. [ ] What's my circuit breaker strategy if the upstream results/odds feed goes down or starts returning garbage?
33. [ ] How do I detect and quarantine a "poisoned" market (bad data corrupting odds) before it causes financial damage?
34. [ ] What's my disaster recovery RPO/RTO for the wallet ledger specifically, separate from the rest of the system?
35. [ ] Do I have a kill switch to freeze all betting platform-wide within seconds, and who/what can trigger it?

## Scalability & Architecture

36. [ ] Monolith vs microservices — and specifically, do wallet and bet-placement need to be separate failure domains from odds and market-data?
37. [ ] Sync REST for bet placement vs event-driven (Kafka) — what's my p99 latency budget for "bet accepted" and does my choice hit it?
38. [ ] How do I horizontally scale the bet-placement path without breaking the per-market serialization I designed in question 8?
39. [ ] What's my read/write split — do live odds reads hit a read replica or Redis cache, while writes go to primary Postgres?
40. [ ] How do I handle hot-partition problems when one market (e.g., a major event) gets 100x the traffic of everything else combined?
41. [ ] Do I need per-market rate limiting/sharding independent of per-user rate limiting?

## Security

42. [ ] How do I prevent a client from spoofing the odds it "saw" to force a favorable bet execution?
43. [ ] What's my defense against a scripted client hammering the bet-placement endpoint faster than a human (bot arbitrage across odds staleness windows)?
44. [ ] How is the admin market-creation/grading panel authenticated and audited — can I prove who graded what and when?
45. [ ] How do I prevent negative-balance exploits via race conditions on the deduct-then-validate path (should be validate-then-deduct, atomically)?
46. [ ] What's my replay-attack protection on the bet-submission API (nonces, signed timestamps)?
47. [ ] Am I logging every state-changing action (bet placed, market graded, balance changed) to an immutable, tamper-evident audit log?

## Observability

48. [ ] What are my key SLIs — bet-placement latency, odds-propagation latency, settlement completion time — and what are the SLOs?
49. [ ] How do I detect a "silent" bug where settlement math is subtly wrong but no error is thrown (property-based invariant checks running in prod)?
50. [ ] Do I have real-time dashboards for platform liability exposure per market, so I'd notice before a $1M loss, not after?
51. [ ] What's my alerting threshold for ledger imbalance (sum of all debits ≠ sum of all credits)?

## Testing

52. [ ] How do I load-test concurrent bet placement to surface the race conditions from question 8–14 before users find them?
53. [ ] Do I have property-based tests generating random bet/settlement sequences to fuzz for payout-math bugs?
54. [ ] How do I test settlement idempotency — deliberately crash the job mid-batch in a test and assert no double-pay?
55. [ ] What's my chaos-engineering plan for the real-time layer (kill a pub/sub node mid-event, verify clients resync correctly)?

## Deployment

56. [ ] How do I guarantee zero-downtime deploys don't drop in-flight bet-placement transactions?
57. [ ] Do I have a staging environment with production-like concurrency load to catch race conditions before they ship?
58. [ ] How do I version my odds/settlement calculation logic so I can prove *which version* computed a given historical payout?

---

## Phasing

- [ ] **MVP (must answer before sign-off):** 1–5, 8–11, 13, 15–19, 21–23, 26–28, 30–31, 35, 37, 42, 45–47
- [ ] **Portfolio depth (answer, but may be stubbed):** 6–7, 12, 20, 24–25, 29, 32–34, 36, 38–41, 43–44, 48–51
- [ ] **README "future work":** 52–58
