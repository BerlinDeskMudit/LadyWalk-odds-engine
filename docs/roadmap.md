# Implementation Roadmap

**260 issues across 15 epics, ordered from the first commit to a defensible production claim.**

This is the *build* track. The 58 questions in [open design questions](design/open-questions.md) are the *design* track: open
decisions that need an artefact to close. The two are kept separate on purpose — a question is
answered by a decision, a task is answered by merged code.

- **Design track** · 58 questions → [open design questions](design/open-questions.md)
- **Build track** · 260 issues → this file

This file is generated from the issue data. CI fails if it drifts from the
issues, so change the source and regenerate rather than editing it by hand.

---

## How to read the ordering

Milestones are a *dependency order*, not a wish list. `B1` is genuinely unblocked work; `B4`
mostly is not. Two rules explain most of the sequencing:

1. **The ledger precedes everything that moves money.** `EP-04` builds the double-entry ledger
   before `EP-05` accepts a bet, because a bet is a ledger movement, not a row insert.
2. **Security and compliance come last, deliberately.** `EP-14` is not neglected — it is
   designed against real components rather than imagined ones. It is still a hard gate before
   anything that could be mistaken for a real operator.

Every issue carries a scope, acceptance criteria that require a *verifiable artefact* (a test
that fails against the pre-fix code, a benchmark, an ADR), and a milestone. No issue closes on
assertion.

## Milestones

| Milestone | Issues | Meaning |
| --- | --: | --- |
| **[B1 · Foundations](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/milestone/4)** | 31 | Tooling, CI, infrastructure, identity. Nothing here touches money, but everything downstream depends on it. |
| **[B2 · Money Path](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/milestone/5)** | 88 | The money path. Money type, double-entry ledger, markets, odds, bet placement, settlement. Highest risk, highest priority. |
| **[B3 · Product](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/milestone/6)** | 61 | Product surface. Real-time delivery, web client, public API, webhooks, SDKs, responsible-gambling surfaces. |
| **[B4 · Hardening](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/milestone/7)** | 80 | Hardening. Observability, audit, reconciliation, chaos testing, DR drills, penetration testing. |
| **Total** | **260** | 15 epics, 195 feature/chore tasks, 50 hardening tasks |

## Epics

| Epic | Milestone | Area | Tasks | Covers |
| --- | --- | --- | --: | --- |
| [EP-01](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/59) Repository, Tooling & Developer Experience | B1 | `tooling` | 15 | Clone to a running, tested, linted, fully type-checked service in under ten minutes — and no money bug can be written in the type system. |
| [EP-02](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/60) Infrastructure as Code & Environments | B1 | `infra` | 11 | Laptop, staging and production are all reproducible from code, and a database restore has actually been drilled, not just documented. |
| [EP-03](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/61) Identity, Access & Administration | B1 | `identity` | 13 | Deny-by-default authorisation, MFA on every admin path, and an attributable record of every privileged action. |
| [EP-04](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/62) Money, Accounts & Double-Entry Ledger | B2 | `data-modeling` | 19 | A `Money` type the compiler will not let you build from a float, and an append-only ledger that provably sums to zero. |
| [EP-05](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/121) Bet Engine | B2 | `settlement` | 20 | Accept, validate, price, and record a bet atomically and idempotently, so a request either produces exactly one ledger-balanced position or nothing at all. |
| [EP-06](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/122) Odds Engine | B2 | `odds-engine` | 18 | Turn probability estimates into displayable, comparable, arithmetically sound prices in every supported format, with the overround always explicit and never silently absorbed. |
| [EP-07](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/123) Settlement & Liabilities | B2 | `settlement` | 20 | Turn a final result into balanced ledger movements and a correct, replayable payout for every bet, with a liability figure that can be independently recomputed from raw bets. |
| [EP-08](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/124) Markets & Feeds | B2 | `data-modeling` | 11 | Own the definition of every market and the ingestion of upstream results, so that market state is derived from a single authoritative source and can always be explained. |
| [EP-09](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/125) Wallet, Balances & KYC Tiering | B2 | `identity` | 16 | Give every account a balance that is a derived, auditable fact of the ledger, and gate deposits and withdrawals by a documented verification tier. |
| [EP-10](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/126) Real-Time Delivery | B3 | `realtime` | 15 | Fan out committed state changes to connected clients over WebSockets with ordering, gap detection, and replayable catch-up, so a client is never silently wrong about a price or a market state. |
| [EP-11](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/127) Web Client | B3 | `frontend` | 20 | Ship an accessible, responsive betting interface whose displayed state is always traceable to a server-confirmed state, and which never implies a bet succeeded before the server has said so. |
| [EP-12](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/128) Public API & Client SDKs | B3 | `api-sdk` | 17 | Expose a versioned, contract-tested public API with honest idempotency, rate limits, and webhooks, so third parties can integrate without the platform being the reason it fails. |
| [EP-13](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/129) Observability & Operations | B3 | `observability` | 15 | Make the system's internal state legible from outside it, through metrics, traces, dashboards, alerts, and runbooks, so an incident is diagnosable from telemetry before it becomes a customer report. |
| [EP-14](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/130) Security, Compliance & Responsible Gambling | B4 | `security` | 20 | Make the security and responsible-gambling controls that a real deployment would be required to demonstrate buildable, testable, and evidenced, so the repository can honestly describe its own compliance posture. |
| [EP-15](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/131) Reliability, Release Engineering & Disaster Recovery | B4 | `release` | 15 | Make releases routine and boring, and make recovery from data loss a rehearsed, timed procedure rather than an improvisation. |

---

## B1 · Foundations

Tooling, CI, infrastructure, identity. Nothing here touches money, but everything downstream depends on it.

### [EP-01](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/59) Repository, Tooling & Developer Experience

**Goal.** Clone to a running, tested, linted, fully type-checked service in under ten minutes — and no money bug can be written in the type system.

15 tasks · 5 critical · 0 hardening · [`EP-01` issue](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/59)

| | Issue | Type | Pri | Title |
| --- | --- | --- | --- | --- |
| T | [F-001](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/63) | `chore` | !! | Initialise the Cargo workspace and enforce dependency direction |
| T | [F-002](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/64) | `feat` | !! | Define the Money type so the compiler forbids floats |
| T | [F-003](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/65) | `chore` | !! | Set up sqlx with compile-time checked queries and offline mode |
| T | [F-004](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/66) | `chore` | ! | Versioned, forward-only SQL migrations with sqlx |
| T | [F-005](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/67) | `chore` | ! | Configure clippy at pedantic with warnings denied, plus supply-chain gates |
| T | [F-006](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/68) | `chore` | ! | Tracing with request-scoped correlation IDs |
| T | [F-007](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/69) | `chore` | ! | Error taxonomy and the HTTP error contract |
| T | [F-008](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/70) | `chore` | ! | Config loading with schema validation and fail-fast startup |
| T | [F-009](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/71) | `feat` | !! | Graceful shutdown with bounded drain, and a test that proves it |
| T | [F-010](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/72) | `chore` | · | One entry point for every recurring task |
| T | [F-011](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/73) | `chore` | ! | Multi-stage distroless images with SBOM and non-root user |
| T | [F-012](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/74) | `feat` | !! | Docker Compose for the whole local environment |
| T | [F-013](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/75) | `feat` | · | Internal service client with mandatory timeouts and opt-in retries |
| T | [F-014](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/76) | `chore` | ! | ADR infrastructure and the four decisions that unlock everything |
| T | [F-015](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/77) | `chore` | · | Devcontainer and editor configuration |

### [EP-02](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/60) Infrastructure as Code & Environments

**Goal.** Laptop, staging and production are all reproducible from code, and a database restore has actually been drilled, not just documented.

11 tasks · 4 critical · 0 hardening · [`EP-02` issue](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/60)

| | Issue | Type | Pri | Title |
| --- | --- | --- | --- | --- |
| T | [F-016](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/78) | `chore` | ! | Terraform baseline: network, subnets, single region |
| T | [F-017](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/79) | `chore` | !! | Managed Postgres with PITR, encryption, and deletion protection |
| T | [F-018](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/80) | `chore` | ! | Redis with an explicit eviction policy and a documented authoritative split |
| T | [F-019](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/81) | `chore` | ! | Object storage for audit segments and exports, with immutability retention |
| T | [F-020](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/82) | `chore` | !! | Key management for signing, encryption, and rotation without a flag day |
| T | [F-021](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/83) | `chore` | ! | Secrets management and per-environment isolation |
| T | [F-022](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/84) | `feat` | ! | Observability infrastructure as code |
| T | [F-023](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/85) | `chore` | ! | Edge: DNS, TLS automation, WAF, and DDoS protection |
| T | [F-024](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/86) | `chore` | !! | Private data tier with no public ingress |
| T | [F-025](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/87) | `chore` | ! | Per-environment state and a promotion path that cannot leak dev into prod |
| T | [F-026](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/88) | `chore` | !! | Verified backup and restore drill |

### [EP-03](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/61) Identity, Access & Administration

**Goal.** Deny-by-default authorisation, MFA on every admin path, and an attributable record of every privileged action.

13 tasks · 4 critical · 0 hardening · [`EP-03` issue](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/61)

| | Issue | Type | Pri | Title |
| --- | --- | --- | --- | --- |
| T | [F-027](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/89) | `feat` | !! | OIDC integration with exported realms and PKCE |
| T | [F-028](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/90) | `feat` | ! | User lifecycle: registration, verification, reset, TOTP MFA |
| T | [F-029](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/91) | `feat` | !! | RBAC with resource-scoped grants and a permission-coverage test |
| T | [F-030](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/92) | `feat` | !! | MFA mandatory on every admin path, plus a break-glass CLI |
| T | [F-031](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/93) | `feat` | !! | Admin action audit with actor, reason, and before/after |
| T | [F-032](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/94) | `feat` | ! | Four-eyes approval for high-value and post-settlement actions |
| T | [F-033](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/95) | `feat` | ! | Service API keys with scopes and overlap rotation |
| T | [F-034](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/96) | `feat` | ! | Account states: freeze, closure, and irreversible self-exclusion |
| T | [F-035](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/97) | `feat` | ! | Responsible-gambling controls: deposit, loss, and session limits |
| T | [F-036](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/98) | `chore` | ! | PII separation with field-level encryption |
| T | [F-037](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/99) | `feat` | · | Session management: list, revoke, and audit |
| T | [F-038](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/100) | `feat` | · | Credential-breach checking via k-anonymity range query |
| T | [F-039](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/101) | `feat` | ! | Rate limiting and abuse controls on the auth endpoints |

---

## B2 · Money Path

The money path. Money type, double-entry ledger, markets, odds, bet placement, settlement. Highest risk, highest priority.

### [EP-04](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/62) Money, Accounts & Double-Entry Ledger

**Goal.** A `Money` type the compiler will not let you build from a float, and an append-only ledger that provably sums to zero.

19 tasks · 11 critical · 0 hardening · [`EP-04` issue](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/62)

| | Issue | Type | Pri | Title |
| --- | --- | --- | --- | --- |
| T | [F-040](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/102) | `feat` | !! | Chart of accounts and the account model |
| T | [F-041](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/103) | `feat` | !! | Append-only ledger_entries schema with immutability enforced in the database |
| T | [F-042](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/104) | `feat` | !! | Double-entry posting API with sum-to-zero enforced in one transaction |
| T | [F-043](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/105) | `feat` | !! | Derived balance projection, materialised from the ledger |
| T | [F-044](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/106) | `feat` | !! | Idempotent posting keyed on reference identity |
| T | [F-045](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/107) | `feat` | !! | Compensating reversal API — corrections never mutate |
| T | [F-046](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/108) | `feat` | !! | Conditional deduction: the fix for TOCTOU and negative balance |
| T | [F-047](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/109) | `feat` | !! | Continuous per-account and per-currency invariant checker |
| T | [F-048](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/110) | `feat` | ! | Rounding policy, per-currency exponents, and explicit precision loss |
| T | [F-049](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/111) | `feat` | !! | Hash-chained audit log with verifiable integrity |
| T | [F-050](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/112) | `feat` | · | Balance snapshot and checkpointing for fast reads |
| T | [F-051](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/113) | `feat` | ! | Fees, commission, and margin postings |
| T | [F-052](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/114) | `feat` | · | FX conversion with an explicit position account |
| T | [F-053](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/115) | `feat` | ! | Deposit path with a pluggable payment adapter |
| T | [F-054](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/116) | `feat` | ! | Withdrawal path with hold, release, and rejection semantics |
| T | [F-055](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/117) | `feat` | !! | Reconciliation engine: ledger vs projection vs provider |
| T | [F-056](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/118) | `feat` | · | Ledger export for audit and dispute resolution |
| T | [F-057](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/119) | `task` | !! | Ledger property-based and fuzz test suite |
| T | [F-058](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/120) | `chore` | ! | Database-level enforcement of ledger invariants |

### [EP-05](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/121) Bet Engine

**Goal.** Accept, validate, price, and record a bet atomically and idempotently, so a request either produces exactly one ledger-balanced position or nothing at all.

20 tasks · 11 critical · 6 hardening · [`EP-05` issue](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/121)

| | Issue | Type | Pri | Title |
| --- | --- | --- | --- | --- |
| T | [F-059](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/132) | `task` | !! | Define the bet lifecycle state machine |
| T | [F-060](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/133) | `feat` | !! | Add a bet idempotency key with a unique constraint |
| T | [F-061](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/134) | `feat` | !! | Enforce bet placement in a single serializable transaction |
| T | [F-062](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/135) | `feat` | !! | Snapshot the accepted odds price onto the bet |
| T | [F-063](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/136) | `task` | ! | Validate and normalize inbound bet requests |
| T | [F-064](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/137) | `feat` | !! | Implement per-bet and per-account stake limits |
| T | [F-065](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/138) | `feat` | ! | Suspend and resume a market against in-flight bets |
| T | [F-066](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/139) | `feat` | ! | Implement bet void and refund |
| T | [F-067](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/140) | `task` | !! | Reject bet requests arriving after market close |
| T | [F-068](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/141) | `feat` | ! | Persist bet request audit records |
| T | [F-069](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/142) | `task` | !! | Write property tests for the acceptance invariants |
| T | [F-070](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/143) | `feat` | ! | Expose bet placement and history over the public API |
| T | [F-071](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/144) | `chore` | · | Add bet placement tracing end to end |
| T | [F-072](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/145) | `feat` | ! | Enforce bet API rate limits per account and per IP |
| H | [H-001](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/277) | `bug` | !! | Harden bet placement against a replayed idempotency key with a mutated payload |
| H | [H-002](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/278) | `bug` | !! | Harden against partial stake debit on transaction abort |
| H | [H-003](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/279) | `bug` | ! | Guard against integer overflow in stake and payout arithmetic |
| H | [H-004](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/280) | `bug` | !! | Prevent a suspended market from accepting a bet through a stale client |
| H | [H-005](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/281) | `bug` | !! | Prevent negative and zero stakes from reaching the ledger |
| H | [H-006](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/282) | `task` | · | Add a bet request size and depth limit |

### [EP-06](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/122) Odds Engine

**Goal.** Turn probability estimates into displayable, comparable, arithmetically sound prices in every supported format, with the overround always explicit and never silently absorbed.

18 tasks · 5 critical · 5 hardening · [`EP-06` issue](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/122)

| | Issue | Type | Pri | Title |
| --- | --- | --- | --- | --- |
| T | [F-073](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/146) | `task` | !! | Write the odds representation ADR |
| T | [F-074](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/147) | `feat` | !! | Implement exact decimal odds arithmetic |
| T | [F-075](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/148) | `feat` | ! | Implement decimal, fractional, and American format conversion |
| T | [F-076](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/149) | `feat` | ! | Convert between odds and implied probability |
| T | [F-077](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/150) | `feat` | !! | Compute the book overround and expose it explicitly |
| T | [F-078](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/151) | `task` | ! | Apply a documented margin policy to pricing |
| T | [F-079](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/152) | `feat` | ! | Build a versioned odds model registry |
| T | [F-080](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/153) | `task` | ! | Implement odds validation and rejection of malformed books |
| T | [F-081](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/154) | `feat` | ! | Snapshot the book for audit and dispute resolution |
| T | [F-082](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/155) | `task` | · | Add odds model evaluation harness |
| T | [F-083](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/156) | `feat` | ! | Publish price change events to the real-time stream |
| T | [F-084](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/157) | `task` | !! | Handle odds model failure without publishing bad prices |
| T | [F-085](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/158) | `task` | ! | Property-test odds arithmetic across the full domain |
| H | [H-007](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/283) | `bug` | ! | Reject odds that round to a value implying certainty |
| H | [H-008](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/284) | `bug` | ! | Handle a book whose implied probabilities sum below one |
| H | [H-009](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/285) | `bug` | !! | Ensure a failed price recomputation cannot publish a stale price as fresh |
| H | [H-010](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/286) | `bug` | ! | Pin the model version so a redeploy cannot change historical prices |
| H | [H-011](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/287) | `bug` | · | Guard decimal parsing against locale and exponent tricks |

### [EP-07](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/123) Settlement & Liabilities

**Goal.** Turn a final result into balanced ledger movements and a correct, replayable payout for every bet, with a liability figure that can be independently recomputed from raw bets.

20 tasks · 10 critical · 8 hardening · [`EP-07` issue](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/123)

| | Issue | Type | Pri | Title |
| --- | --- | --- | --- | --- |
| T | [F-086](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/159) | `task` | !! | Define the settlement model ADR |
| T | [F-087](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/160) | `feat` | !! | Implement exactly-once settlement processing |
| T | [F-088](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/161) | `feat` | !! | Compute payouts from the frozen price snapshot |
| T | [F-089](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/162) | `feat` | !! | Write ledger entries atomically with settlement |
| T | [F-090](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/163) | `feat` | ! | Compute the liability figure and persist it |
| T | [F-091](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/164) | `feat` | ! | Run a scheduled liability reconciliation |
| T | [F-092](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/165) | `feat` | ! | Handle void, abandoned, and postponed results |
| T | [F-093](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/166) | `feat` | !! | Build a deterministic settlement replay tool |
| T | [F-094](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/167) | `task` | ! | Settle a result set in a single bounded transaction |
| T | [F-095](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/168) | `feat` | · | Expose settlement status and payout history |
| T | [F-096](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/169) | `task` | · | Reconcile payouts against feed-provided totals |
| T | [F-097](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/170) | `task` | !! | Property-test settlement invariants |
| H | [H-012](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/288) | `bug` | !! | Prevent a result correcting an already-settled bet silently |
| H | [H-013](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/289) | `bug` | !! | Guarantee a payout is never issued twice under concurrent workers |
| H | [H-014](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/290) | `bug` | !! | Handle a settlement run that dies partway through a result set |
| H | [H-015](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/291) | `bug` | ! | Cap a runaway payout and surface it as an anomaly |
| H | [H-016](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/292) | `bug` | ! | Make void refunds idempotent under concurrent requests |
| H | [H-017](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/293) | `bug` | ! | Prevent a reconciliation job from masking a real divergence |
| H | [H-018](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/294) | `bug` | ! | Reject a result whose selection set does not match the market |
| H | [H-019](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/305) | `bug` | !! | Prevent settlement of a bet accepted after the market closed |

### [EP-08](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/124) Markets & Feeds

**Goal.** Own the definition of every market and the ingestion of upstream results, so that market state is derived from a single authoritative source and can always be explained.

11 tasks · 3 critical · 0 hardening · [`EP-08` issue](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/124)

| | Issue | Type | Pri | Title |
| --- | --- | --- | --- | --- |
| T | [F-098](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/171) | `feat` | ! | Model market types with a registry |
| T | [F-099](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/172) | `task` | !! | Enforce market state as a single authoritative path |
| T | [F-100](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/173) | `feat` | ! | Build a feed adapter interface with a mock implementation |
| T | [F-101](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/174) | `feat` | !! | Implement feed reconnection with backoff and state resync |
| T | [F-102](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/175) | `feat` | !! | Detect feed stalls and suspend affected markets |
| T | [F-103](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/176) | `task` | ! | Normalise and deduplicate inbound feed messages |
| T | [F-104](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/177) | `feat` | · | Add a feed health and lag dashboard |
| T | [F-105](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/178) | `feat` | · | Support scheduled and event-driven market creation |
| T | [F-106](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/179) | `feat` | · | Implement market reopen and postponed event handling |
| T | [F-107](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/180) | `task` | · | Version market definitions |
| T | [F-108](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/181) | `task` | ! | Enforce timezone correctness for market boundaries |

### [EP-09](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/125) Wallet, Balances & KYC Tiering

**Goal.** Give every account a balance that is a derived, auditable fact of the ledger, and gate deposits and withdrawals by a documented verification tier.

16 tasks · 7 critical · 5 hardening · [`EP-09` issue](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/125)

| | Issue | Type | Pri | Title |
| --- | --- | --- | --- | --- |
| T | [F-109](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/182) | `feat` | !! | Project account balances from the ledger |
| T | [F-110](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/183) | `task` | ! | Separate available, reserved, and pending balances |
| T | [F-111](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/184) | `feat` | !! | Implement a double-entry ledger constraint in the database |
| T | [F-112](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/185) | `feat` | ! | Implement deposits with a documented state machine |
| T | [F-113](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/186) | `feat` | ! | Implement withdrawals with hold, review, and release |
| T | [F-114](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/187) | `feat` | ! | Enforce KYC tier limits on deposit and withdrawal |
| T | [F-115](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/188) | `feat` | ! | Record a consent and verification audit trail |
| T | [F-116](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/189) | `feat` | !! | Self-exclude and close accounts irreversibly |
| T | [F-117](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/190) | `task` | · | Expose the wallet as a ledger view, not a stored total |
| T | [F-118](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/191) | `feat` | ! | Add deposit and withdrawal reconciliation |
| T | [F-119](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/192) | `task` | !! | Prevent PII from entering logs and analytics |
| H | [H-020](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/306) | `bug` | !! | Prevent a balance projection from drifting from the ledger |
| H | [H-021](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/307) | `bug` | !! | Make a provider webhook replay a no-op |
| H | [H-022](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/308) | `bug` | ! | Enforce withdrawal limits atomically with the reservation |
| H | [H-023](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/309) | `bug` | !! | Prevent a self-exclusion from being bypassed by an open session |
| H | [H-024](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/310) | `bug` | ! | Redact personal data from support tooling and exports |

---

## B3 · Product

Product surface. Real-time delivery, web client, public API, webhooks, SDKs, responsible-gambling surfaces.

### [EP-10](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/126) Real-Time Delivery

**Goal.** Fan out committed state changes to connected clients over WebSockets with ordering, gap detection, and replayable catch-up, so a client is never silently wrong about a price or a market state.

15 tasks · 2 critical · 4 hardening · [`EP-10` issue](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/126)

| | Issue | Type | Pri | Title |
| --- | --- | --- | --- | --- |
| T | [F-120](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/193) | `task` | ! | Write the real-time delivery ADR |
| T | [F-121](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/194) | `feat` | ! | Publish committed changes onto a durable stream |
| T | [F-122](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/195) | `feat` | ! | Implement the WebSocket gateway with heartbeat and backpressure |
| T | [F-123](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/196) | `feat` | !! | Add per-market sequence numbers and gap detection |
| T | [F-124](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/197) | `feat` | ! | Implement reconnect with catch-up from a sequence |
| T | [F-125](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/198) | `feat` | · | Apply topic-level subscription filtering |
| T | [F-126](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/199) | `feat` | ! | Rate limit and authenticate WebSocket connections |
| T | [F-127](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/200) | `task` | ! | Load-test the gateway at target fan-out |
| T | [F-128](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/209) | `feat` | ! | Instrument delivery lag end to end |
| T | [F-129](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/210) | `feat` | ! | Deliver events during a partial node failure |
| T | [F-130](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/211) | `task` | · | Version the real-time event schema |
| H | [H-025](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/311) | `bug` | ! | Handle a client that silently stops consuming the stream |
| H | [H-026](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/312) | `bug` | !! | Prevent out-of-order event delivery from showing a wrong price |
| H | [H-027](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/313) | `bug` | ! | Prevent a WebSocket upgrade from bypassing rate limits |
| H | [H-028](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/314) | `bug` | · | Bound client subscription sets |

### [EP-11](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/127) Web Client

**Goal.** Ship an accessible, responsive betting interface whose displayed state is always traceable to a server-confirmed state, and which never implies a bet succeeded before the server has said so.

20 tasks · 4 critical · 5 hardening · [`EP-11` issue](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/127)

| | Issue | Type | Pri | Title |
| --- | --- | --- | --- | --- |
| T | [F-131](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/212) | `chore` | ! | Scaffold the web client and its build pipeline |
| T | [F-132](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/213) | `feat` | ! | Build the live odds board with honest staleness |
| T | [F-133](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/214) | `feat` | !! | Build the betslip with server-confirmed placement |
| T | [F-134](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/215) | `feat` | ! | Build bet history and account balance views |
| T | [F-135](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/216) | `feat` | ! | Build the admin panel with role-gated actions |
| T | [F-136](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/217) | `feat` | ! | Handle connection loss and reconnection in the client |
| T | [F-137](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/218) | `feat` | · | Add an offline-tolerant bet submission queue |
| T | [F-138](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/219) | `task` | ! | Meet WCAG 2.2 AA across critical flows |
| T | [F-139](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/220) | `feat` | · | Support multiple locales, currencies, and timezones |
| T | [F-140](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/221) | `task` | · | Add an error boundary and failure reporting |
| T | [F-141](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/222) | `task` | !! | Add end-to-end tests for the critical user journeys |
| T | [F-142](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/223) | `task` | · | Enforce client performance budgets in CI |
| T | [F-143](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/224) | `task` | · | Add a design system for money, odds, and status |
| T | [F-144](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/225) | `feat` | ! | Display responsible-gambling surfaces in the flow |
| T | [F-145](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/226) | `task` | ! | Add client security headers and a strict content policy |
| H | [H-029](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/315) | `bug` | !! | Prevent an optimistic bet confirmation that the server later rejects |
| H | [H-030](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/316) | `bug` | !! | Prevent a stale price from being displayed as live after a disconnect |
| H | [H-031](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/317) | `bug` | ! | Prevent a queued offline bet from being silently dropped |
| H | [H-032](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/318) | `bug` | ! | Prevent a client-side money rounding difference from the server |
| H | [H-033](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/319) | `bug` | ! | Prevent the client bundle from leaking secrets or internal URLs |

### [EP-12](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/128) Public API & Client SDKs

**Goal.** Expose a versioned, contract-tested public API with honest idempotency, rate limits, and webhooks, so third parties can integrate without the platform being the reason it fails.

17 tasks · 2 critical · 4 hardening · [`EP-12` issue](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/128)

| | Issue | Type | Pri | Title |
| --- | --- | --- | --- | --- |
| T | [F-146](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/227) | `task` | ! | Define the public API surface and versioning policy |
| T | [F-147](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/228) | `task` | ! | Specify the API as an OpenAPI contract |
| T | [F-148](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/229) | `task` | !! | Enforce the API contract with automated tests |
| T | [F-149](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/230) | `task` | ! | Add pagination to every collection endpoint |
| T | [F-150](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/231) | `task` | ! | Standardise the API error envelope |
| T | [F-151](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/232) | `task` | ! | Apply API versioning with parallel version support |
| T | [F-152](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/233) | `feat` | ! | Deliver signed outbound webhooks with retry |
| T | [F-153](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/234) | `feat` | · | Ship a typed client SDK generated from the contract |
| T | [F-154](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/235) | `feat` | ! | Enforce per-client rate limits and quotas |
| T | [F-155](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/236) | `feat` | ! | Add a sandbox environment with synthetic data only |
| T | [F-156](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/237) | `task` | ! | Authenticate clients per the identity ADR |
| T | [F-157](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/238) | `task` | · | Version and document the event catalogue |
| T | [F-158](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/239) | `task` | · | Load-test the public API at target throughput |
| H | [H-034](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/320) | `bug` | ! | Prevent pagination from skipping records under concurrent writes |
| H | [H-035](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/321) | `bug` | !! | Prevent a webhook signature from being computed over a mutable payload |
| H | [H-036](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/322) | `bug` | ! | Prevent an error response from leaking internal detail |
| H | [H-037](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/323) | `bug` | ! | Prevent a replayed API request from double-charging a client |

### [EP-13](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/129) Observability & Operations

**Goal.** Make the system's internal state legible from outside it, through metrics, traces, dashboards, alerts, and runbooks, so an incident is diagnosable from telemetry before it becomes a customer report.

15 tasks · 2 critical · 3 hardening · [`EP-13` issue](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/129)

| | Issue | Type | Pri | Title |
| --- | --- | --- | --- | --- |
| T | [F-159](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/240) | `feat` | ! | Instrument the services with OpenTelemetry |
| T | [F-160](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/241) | `task` | !! | Define service-level indicators for the money path |
| T | [F-161](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/242) | `feat` | ! | Build dashboards per service and per journey |
| T | [F-162](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/243) | `feat` | ! | Alert on symptoms with routed ownership |
| T | [F-163](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/244) | `task` | ! | Write runbooks for the top failure modes |
| T | [F-164](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/245) | `chore` | ! | Centralise structured logging |
| T | [F-165](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/246) | `task` | · | Build a metrics catalogue and cardinality budget |
| T | [F-166](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/247) | `feat` | ! | Expose health and readiness endpoints |
| T | [F-167](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/248) | `feat` | !! | Monitor background worker health and backlog |
| T | [F-168](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/249) | `feat` | · | Add a support tooling view of a customer account |
| T | [F-169](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/250) | `chore` | · | Runbooks and alerts from a game day |
| T | [F-170](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/251) | `task` | · | Track deployment and change events in telemetry |
| H | [H-038](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/324) | `bug` | ! | Prevent unbounded metric label cardinality |
| H | [H-039](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/325) | `task` | ! | Prevent a monitoring outage from being invisible |
| H | [H-040](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/326) | `bug` | · | Prevent trace context from being lost across the job queue |

---

## B4 · Hardening

Hardening. Observability, audit, reconciliation, chaos testing, DR drills, penetration testing.

### [EP-14](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/130) Security, Compliance & Responsible Gambling

**Goal.** Make the security and responsible-gambling controls that a real deployment would be required to demonstrate buildable, testable, and evidenced, so the repository can honestly describe its own compliance posture.

20 tasks · 8 critical · 7 hardening · [`EP-14` issue](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/130)

| | Issue | Type | Pri | Title |
| --- | --- | --- | --- | --- |
| T | [F-171](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/252) | `feat` | ! | Implement authentication with standards-based OIDC |
| T | [F-172](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/253) | `feat` | !! | Enforce role-based access control on every endpoint |
| T | [F-173](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/254) | `feat` | ! | Require step-up authentication for privileged actions |
| T | [F-174](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/255) | `feat` | !! | Implement an append-only audit trail for privileged actions |
| T | [F-175](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/256) | `chore` | !! | Centralise secrets management with no secrets in the repository |
| T | [F-176](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/257) | `task` | ! | Enforce TLS everywhere and verify certificate handling |
| T | [F-177](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/258) | `chore` | ! | Add a dependency vulnerability scan to CI |
| T | [F-178](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/259) | `chore` | ! | Run static analysis with security lints in CI |
| T | [F-179](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/260) | `task` | !! | Complete a threat model for the money path |
| T | [F-180](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/261) | `feat` | ! | Add abuse and fraud detection on the money path |
| T | [F-181](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/262) | `feat` | · | Implement deposit and play monitoring reports |
| T | [F-182](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/263) | `task` | ! | Document the responsible-gambling control set |
| T | [F-183](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/264) | `task` | !! | Run an independent penetration test |
| H | [H-041](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/327) | `bug` | !! | Prevent privilege escalation through a role check on the client |
| H | [H-042](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/328) | `bug` | !! | Prevent a session from outliving a revocation |
| H | [H-043](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/201) | `bug` | !! | Prevent an audit record from being altered or removed |
| H | [H-044](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/202) | `chore` | ! | Prevent secrets from appearing in build logs and CI output |
| H | [H-045](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/203) | `chore` | · | Prevent a vulnerability exception from becoming permanent |
| H | [H-046](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/204) | `bug` | · | Prevent timestamp manipulation from affecting outcome ordering |
| H | [H-047](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/205) | `bug` | ! | Prevent duplicate accounts from defeating per-account limits |

### [EP-15](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/131) Reliability, Release Engineering & Disaster Recovery

**Goal.** Make releases routine and boring, and make recovery from data loss a rehearsed, timed procedure rather than an improvisation.

15 tasks · 6 critical · 3 hardening · [`EP-15` issue](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/131)

| | Issue | Type | Pri | Title |
| --- | --- | --- | --- | --- |
| T | [F-184](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/265) | `chore` | ! | Containerise all services |
| T | [F-185](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/266) | `chore` | ! | Stand up the full stack locally with one command |
| T | [F-186](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/267) | `chore` | !! | Implement a complete CI pipeline |
| T | [F-187](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/268) | `chore` | ! | Build and push immutable image tags with provenance |
| T | [F-188](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/269) | `chore` | ! | Define infrastructure as code for all environments |
| T | [F-189](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/270) | `task` | !! | Implement database migrations with zero-downtime discipline |
| T | [F-190](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/271) | `feat` | !! | Implement automated backup and verified restore |
| T | [F-191](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/272) | `task` | !! | Document and rehearse the disaster recovery runbook |
| T | [F-192](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/273) | `task` | ! | Test graceful degradation of each dependency |
| T | [F-193](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/274) | `feat` | ! | Implement a background migration tool with a dry run |
| T | [F-194](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/275) | `feat` | ! | Automate release promotion with rollback |
| T | [F-195](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/276) | `task` | ! | Run a load and stress test at target scale |
| H | [H-048](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/206) | `bug` | !! | Prevent a failed migration from leaving the schema unusable |
| H | [H-049](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/207) | `bug` | !! | Prevent an untested backup from being relied upon |
| H | [H-050](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/208) | `bug` | ! | Prevent a rollback from reverting the schema past the data it needs |

---

## Hardening backlog

50 tasks (`H-001`–`H-050`) that are not features but failure modes: races, replay, partial
failure, arithmetic edges, and the specific ways a betting platform leaks money. They are
attached to the epic they threaten rather than collected in a separate bucket, because a
hardening task with no owning component never gets done.

| Area | Count | Examples |
| --- | --: | --- |
| `security` | 8 | privilege escalation via client-supplied role, audit tampering, PII in logs |
| `concurrency` | 6 | double-payout under concurrent workers, request replay with a mutated payload |
| `data-modeling` | 5 | integer overflow in money arithmetic, unsigned-tenant constraint gaps |
| `reliability` | 5 | untested backups, partially-failed migrations, rollback past the schema |
| `frontend` | 4 | optimistic bet confirmation, stale price shown as live, client money rounding |
| `settlement` | 3 | result correcting an already-settled bet, void refunds, reconciliation that cannot mask divergence |
| `realtime` | 3 | out-of-order events showing a wrong price, silent non-consuming clients |
| `odds-engine` | 3 |  |
| `identity` | 3 | session outliving revocation, duplicate accounts defeating per-account limits |
| `api-sdk` | 3 | webhook signature over mutable bytes, pagination skipping records, error leakage |
| `observability` | 3 | unbounded metric cardinality, lost trace context across the queue |
| `tooling` | 2 | secrets in build logs, vulnerability exceptions that never expire |
| `testing` | 1 | arithmetic edge coverage, fault injection at each commit point |
| `release` | 1 | rollback incompatible with the deployed schema |

## Technology decisions

Committed in [Architecture](architecture/README.md) and realised by the `B1` tasks. The two that
constrain everything else:

| Decision | Choice | Why |
| --- | --- | --- |
| Backend language | **Rust, single language** | One `Money` type across the whole system. A second language puts a conversion boundary on the one value that must never be converted. |
| Data access | **sqlx, compile-time checked** | Queries are verified against the schema at build time. `.sqlx/` is committed so CI verifies it too — a query can never drift from the schema unnoticed. |
| Money representation | `i64` minor units, newtype | A float cannot be constructed without an explicit, audited conversion. |
| Ledger enforcement | Application **and** PostgreSQL constraints | The application is the fast path; the constraint is what actually holds when someone opens a SQL client. |

Supporting stack: Tokio · Axum/Tower · PostgreSQL 16 · Redis Streams · WebSockets · Next.js/TypeScript ·
Keycloak/OIDC · OpenTelemetry/Prometheus/Grafana/Loki · proptest · testcontainers-rs · k6 · Toxiproxy ·
GitHub Actions · Terraform · Docker Compose.

Explicitly **not** chosen, and why:

| Rejected | Reason |
| --- | --- |
| Kafka | Operational cost is not justified before a real event-volume ceiling is known. Redis Streams plus a Postgres job queue with `FOR UPDATE SKIP LOCKED` cover the actual load. |
| An ORM | The money path needs the exact SQL, the exact isolation level, and constraints the ORM will not express. Hand-written sqlx keeps that visible. |
| Kubernetes | The operational surface is larger than the application. Containers plus Terraform cover every environment this project can honestly run. |
| Go alongside Rust | A second backend language reintroduces the `Money` conversion boundary the single-language choice exists to remove. |

## Issue scheme

| Prefix | Count | Meaning |
| --- | --: | --- |
| `EP-01`–`EP-15` | 15 | Epics. Each links its tasks as a checklist. |
| `F-001`–`F-195` | 195 | Feature, task, and chore work. |
| `H-001`–`H-050` | 50 | Hardening and failure-mode work, attached to the epic it threatens. |
| `Q01`–`Q58` | 58 | The design track, in [open design questions](design/open-questions.md). |

**Labels.** Every build issue has exactly one `type:` (`feat` `bug` `task` `chore` `epic`), one
`area:`, one `priority:` (`critical` `high` `medium` `low`), the `track:build` label, and a
milestone.

**Type split:** 46 `bug` · 37 `chore` · 15 `epic` · 108 `feat` · 54 `task`

**Priority split:** 84 `critical` · 123 `high` · 37 `medium` · 1 `low`

## How to claim work

1. Comment on the issue to claim it. One issue, one person.
2. Check `blocked_by` in the issue body. If the blocker is open, pick something else.
3. Open a PR that satisfies every acceptance criterion. Each criterion names a checkable artefact.
4. The issue closes when the artefact is merged — a test that fails without the change, a
   benchmark with numbers, or an ADR that records a decision and its consequences.

If a design question blocks you, it is a `Q` issue. Answer it in an ADR, close it with the
link, and the dependent tasks unblock.
