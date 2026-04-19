---
name: tests-required
description: Use when implementing any feature, bugfix, or refactor — tentacles requires automated tests that exist and pass for any claim of completion, but does NOT require writing them first.
---

# Tests Required (Not Test-First)

Every piece of behavior an implementer produces must be covered by an automated test before it can be claimed complete. Tentacles deliberately does **not** mandate test-first (RED-GREEN-REFACTOR) discipline — implementers may write tests alongside, after, or iteratively with implementation as long as the finished task ships with passing tests.

## The Rule

A task is complete only when **all three** are true:

1. Automated tests exist that exercise the new or changed behavior.
2. Those tests pass locally (and in CI when CI applies).
3. The existing test suite still passes.

If any of those fails, the task is not complete. Do not claim completion, do not request review, do not merge.

## What Counts as a Test

- Unit tests, integration tests, end-to-end tests — whatever level actually verifies the behavior. Pick the lowest level that can observe what changed.
- The test must fail if the implementation is wrong. Tests that pass regardless of implementation (e.g., asserting only that a function exists) don't count.
- Snapshot tests count only if the snapshot was reviewed by a human or agent who understood the expected output.

## What Does Not Count

- Manual smoke checks ("I ran it and it looked fine").
- Type checks alone. Types verify shape, not behavior.
- Logs/print statements in the implementation confirming "it ran."
- A passing CI badge from before your change.

## Test-First Is Allowed But Not Required

If you prefer to write the failing test first, do so — it's a perfectly good way to think. Tentacles just doesn't require it. The requirement is on the **finished state**, not the ordering of keystrokes. This trades some of TDD's design pressure for subagent autonomy; the `parallel-subagent-execution` review loop is where test adequacy gets checked.

## Scope Boundaries

- If the change is to test infrastructure itself (a new fixture, a helper, a test runner config), the test of the test infrastructure is that downstream tests pass using it.
- If the change is documentation-only or purely cosmetic (whitespace, comment rewording), no new tests are required — but existing tests must still pass.
- If a change is genuinely untestable (e.g., a manual deployment config you cannot run locally), call that out explicitly in the task result and explain what you verified manually. Do not silently skip.

## Red Flags

| Thought | Reality |
|---------|---------|
| "The existing tests cover this" | Verify by breaking the implementation and confirming a test fails. |
| "I'll add the test after review" | The task isn't complete yet. Don't request review. |
| "This is too simple to need a test" | That's how regressions enter. Add the test. |
| "Tests are the reviewer's job" | No — reviewers check test adequacy, they don't write the first test. |
| "I ran it manually and it works" | Manual = not automated = not a test. |

## Interaction With Other Skills

- `verification-before-completion` — running the test suite is one of the verification steps; this skill tells you *which* evidence is required.
- `requesting-code-review` / `receiving-code-review` — reviewers will check test adequacy. Expect pushback on weak assertions or missing edge cases.
- `parallel-subagent-execution` — each implementer subagent is expected to satisfy this skill before marking its task done.
