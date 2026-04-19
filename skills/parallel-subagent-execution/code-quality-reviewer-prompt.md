# Code Quality Reviewer Prompt Template

Use this template when dispatching a code quality reviewer for a parallel task.

**Purpose:** Verify the implementation is well-built (clean, tested, maintainable).

**Only dispatch after spec compliance review passes ✅**

**Parameters the coordinator fills in:**
- `WORKTREE_PATH` — the `path` string returned by the implementer Agent call (the implementer's isolated worktree). Paste it in place of `[WORKTREE_PATH]` below.

**Dispatch without `isolation: "worktree"`.** The reviewer must read the implementer's existing worktree, not create a new one.

```
Task tool (superpowers:code-reviewer):
  Use template at requesting-code-review/code-reviewer.md

  WHAT_WAS_IMPLEMENTED: [from implementer's report]
  PLAN_OR_REQUIREMENTS: task [task-id] Execution Graph entry
  BASE_SHA: [commit before task — run `git log --oneline` in [WORKTREE_PATH] to find it]
  HEAD_SHA: [run `git rev-parse HEAD` in [WORKTREE_PATH]]
  DESCRIPTION: [task summary]
```

**Before reviewing, read the implementer's rationale:**

```bash
cd [WORKTREE_PATH]
git log -1 --format="%B"
```

Use the rationale to evaluate whether design choices are sound, not just whether the code is syntactically clean. A choice that looks odd may be well-justified — or the rationale may reveal a misunderstanding worth flagging.

**Additional checks beyond standard code quality:**
- Does each file have one clear responsibility with a well-defined interface?
- Are units decomposed so they can be understood and tested independently?
- Did this change create new files that are already large, or significantly grow existing files? (Focus on what this change contributed — don't flag pre-existing sizes.)
- Do tests verify behavior, not just mock behavior?

**Returns:** Strengths, Issues (Critical/Important/Minor), Assessment
