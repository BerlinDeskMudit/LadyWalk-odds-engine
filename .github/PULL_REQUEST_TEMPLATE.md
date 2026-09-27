<!--
Keep this file short. A long template gets skipped, and a skipped template is
worse than no template. If a section does not apply, delete it rather than
writing "N/A" in every box.
-->

## What this changes

<!-- One paragraph. What is different after this merges, and why now. -->

Closes #

## Type of change

- [ ] Bug fix — a defect that could reach production
- [ ] Feature — new behaviour
- [ ] Hardening — a failure mode made survivable
- [ ] Refactor — no behaviour change
- [ ] Docs / ADR / tooling
- [ ] Dependency bump

## Acceptance criteria

<!--
Tick these in the PR, not in the issue. The issue body is the source of truth and
should stay stable so it remains readable as a record of what was agreed.
-->

- [ ]

## The artefact

<!--
An issue closes when an artefact is committed, not when a paragraph is typed.
Which of these does this PR add? Delete the rest.
-->

- [ ] **ADR** in `docs/adr/` — the decision, the alternatives, and what was rejected
- [ ] **Test** that fails against the pre-change code and passes after
- [ ] **Benchmark or measurement** committed alongside the change
- [ ] **Runbook** under `runbooks/`, plus a drill result

Link it:

## Checklist

- [ ] `cargo fmt --check`, `cargo clippy --all-targets -- -D warnings`, and `cargo test` pass locally
- [ ] `cargo sqlx prepare` re-run if any query changed, and `.sqlx/` is committed
- [ ] No float in a money path — the CI gate checks this, and it is not advisory
- [ ] Public API, schema, or wire-format change: documented in the same PR
- [ ] New or changed behaviour has a test that would fail without this change
- [ ] Commit messages follow Conventional Commits, with a scope matching the `area:` label

## Risk and rollback

<!--
What breaks if this is wrong, how would you notice, and how do you undo it?
A change to the money path that cannot be rolled back needs a stated plan.
-->

**Risk:**

**Detected by:**

**Rollback:**
