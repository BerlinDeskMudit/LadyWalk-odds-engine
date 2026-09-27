# Architecture decision records

An ADR captures one architectural decision, the alternatives that were
rejected, and — the part that matters most — the condition that should make
someone reopen it.

**This directory is currently empty.** That is the honest state of the project:
no design question has been answered yet. The first ADRs will be written by
answering the questions tracked in [`../design/open-questions.md`](../design/open-questions.md),
not in advance of them.

## Process

1. **Claim the question.** Comment on the issue to claim it.
2. **Copy the template.** `0000-template.md` → `NNNN-kebab-title.md`, numbered
   sequentially from `0001`. Never reuse a number, and never renumber.
3. **Open a PR** with the ADR in `Proposed` status. It is reviewed like code,
   because a decision that constrains 200 issues is more expensive to change
   later than most code is to write.
4. **Merge to accept it.** Merging an ADR sets the status to `Accepted` and
   closes the issue it answers.
5. **Supersede, do not edit.** A merged ADR is immutable. To change a decision,
   write a new ADR that links to and quotes the one it replaces, and set the old
   one's status to `Superseded by ADR-NNNN`. History is the point; an ADR that
   can be rewritten proves nothing.

## Index

| ADR | Title | Status | Date |
| --- | --- | --- | --- |
| — | _none yet_ | | |

## Conventions

- One decision per file. If it needs an "and", it is two ADRs.
- The title is a statement, not a topic: `0001-represent-money-as-i64-minor-units`,
  not `0001-money`.
- **`Revisit if` is mandatory.** A decision without a measurable trigger for
  reversal is a decision that cannot be corrected. If you cannot state the
  trigger, you have not finished deciding.
- Record the option you rejected *and why*. Six months later, the rejected option
  is usually the one somebody proposes again, and the reason it lost is the only
  thing that stops the cycle.

## Candidates

The committed constraints in
[`../architecture/README.md`](../architecture/README.md) are written as if they
were already decided, and each will become an ADR when its question is answered:

| Constraint | Question |
| --- | --- |
| Money is never a float | [Q01](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/1) |
| The ledger is the source of truth, not the balance | [Q03](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/3) |
| Double-entry sums to zero with zero tolerance | [Q04](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/4) |
| The server decides the price | [Q19](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/19) |
| Every state change is audited tamper-evidently | [Q47](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/47) |
| One switch stops all money movement | [Q35](https://github.com/BerlinDeskMudit/LadyWalk-odds-engine/issues/35) |
