---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Writing Plans

## Overview

Write comprehensive implementation plans assuming the engineer has zero context for our codebase and questionable taste. Document everything they need to know: which files to touch for each task, constraints, testing, docs they might need to check, how to test it. Give them the whole plan as bite-sized tasks. DRY. YAGNI. Frequent commits.

Assume they are a skilled developer, but know almost nothing about our toolset or problem domain. Assume they don't know good test design very well.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Context:** This should be run in a dedicated worktree (created by brainstorming skill).

**Save plans to:** `docs/superpowers/plans/YYYY-MM-DD-<feature-name>.md`
- (User preferences for plan location override this default)

## Scope Check

If the spec covers multiple independent subsystems, it should have been broken into sub-project specs during brainstorming. If it wasn't, suggest breaking this into separate plans — one per subsystem. Each plan should produce working, testable software on its own.

## File Structure

Before defining tasks, map out which files will be created or modified and what each one is responsible for. This is where decomposition decisions get locked in.

- Design units with clear boundaries and well-defined interfaces. Each file should have one clear responsibility.
- You reason best about code you can hold in context at once, and your edits are more reliable when files are focused. Prefer smaller, focused files over large ones that do too much.
- Files that change together should live together. Split by responsibility, not by technical layer.
- In existing codebases, follow established patterns. If the codebase uses large files, don't unilaterally restructure - but if a file you're modifying has grown unwieldy, including a split in the plan is reasonable.

This structure informs the task decomposition. Each task should produce self-contained changes that make sense independently.

## Execution Graph

Every plan MUST include an **Execution Graph** section after the File Structure. The Execution Graph defines which tasks can run in parallel and what detail level each implementer receives.

### Parallel Groups

Tasks are assigned to lettered groups (A, B, C…). All tasks in a group are independent and can run concurrently. Group B runs only after all Group A tasks complete and merge. Assign groups based on dependency analysis: if task X must complete before task Y can start, Y goes in a later group.

**Err toward parallelization.** If two tasks touch different files and aren't logically dependent, put them in the same group. Merge conflicts, if they occur, are handled by a conflict-resolution agent — they are not a reason to serialize work.

### Detail Tiers

Each task is assigned a detail tier based on how much structure the implementer needs:

**contract** — Use at integration points shared between parallel agents, or anywhere two agents must implement against a shared interface. Provide explicit types, function signatures, and method names. Two agents implementing against a vague interface will diverge; contracts prevent this.

**skeleton+intent** — Use where meaningful design choices exist. Provide function signatures and a description of the algorithm or invariants. The agent decides the internals.

**acceptance-criteria** — Use for isolated utilities with obvious shape. Provide behavioral spec (inputs, outputs, edge cases, errors). No structure prescribed.

### Task Metadata Format

```markdown
## Execution Graph

### Task: <task-id>
- **Depends on**: <task-id>, ... | none
- **Parallel group**: A | B | C ...
- **Detail tier**: contract | skeleton+intent | acceptance-criteria
- **Content** *(varies by tier)*:
  - If `contract`: `**Contract:** <explicit types/interfaces/signatures>`
  - If `skeleton+intent`: `**Skeleton + intent:** <function signatures + invariant description>`
  - If `acceptance-criteria`: *(omit this field — acceptance criteria below is sufficient)*
- **Acceptance criteria**: <behavioral spec — always present regardless of tier>
```

## Plan Document Header

**Every plan MUST start with this header:**

```markdown
# [Feature Name] Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: If this plan has an Execution Graph, use `superpowers:parallel-subagent-execution`. Otherwise use `superpowers:subagent-driven-development`. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

## Bite-Sized Task Granularity

**Each step is one action (2-5 minutes):**
- "Write the failing test" - step
- "Run it to make sure it fails" - step
- "Implement the minimal code to make the test pass" - step
- "Run the tests and make sure they pass" - step
- "Commit" - step

The above shows a code task. Non-code tasks (markdown edits, configuration, schema changes) use equivalent-granularity steps appropriate to their type. For Execution Graph plans, each task's steps need only cover what the assigned **detail tier** requires — not full implementations. (See **Execution Graph → Detail Tiers** above for what each tier's steps should contain.)

## Task Structure

````markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

- [ ] **Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

- [ ] **Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

- [ ] **Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

- [ ] **Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

- [ ] **Step 5: Commit**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
````

## No Placeholders

Every step must contain the actual content an engineer needs. These are **plan failures** — never write them:
- "TBD", "TODO", "implement later", "fill in details"
- "Add appropriate error handling" / "add validation" / "handle edge cases"
- "Write tests for the above" without showing what to test (for tasks that require tests)
- "Similar to Task N" (repeat the code — the engineer may be reading tasks out of order)
- Steps that describe what to do without showing how (code blocks required for code steps)
- References to types, functions, or methods not defined in any task

## Remember
- Exact file paths always
- Complete code in every step — if a step changes code, show the code
- Exact commands with expected output
- DRY, YAGNI, frequent commits

## Self-Review

After writing the complete plan, look at the spec with fresh eyes and check the plan against it. This is a checklist you run yourself — not a subagent dispatch.

**1. Spec coverage:** Skim each section/requirement in the spec. Can you point to a task that implements it? List any gaps.

**2. Placeholder scan:** Search your plan for red flags — any of the patterns from the "No Placeholders" section above. Fix them.

**3. Type consistency:** Do the types, method signatures, and property names you used in later tasks match what you defined in earlier tasks? A function called `clearLayers()` in Task 3 but `clearFullLayers()` in Task 7 is a bug.

**4. Execution Graph coverage:** Does every task in the plan appear in the Execution Graph? Does every Execution Graph task have a corresponding plan task?

If you find issues, fix them inline. No need to re-review — just fix and move on.

## Execution Handoff

After saving the plan:

**"Plan complete and saved to `docs/superpowers/plans/<filename>.md`.**

**Execution:** Use `superpowers:parallel-subagent-execution` if this plan has an Execution Graph (recommended for most plans). Use `superpowers:subagent-driven-development` for sequential-only plans."
