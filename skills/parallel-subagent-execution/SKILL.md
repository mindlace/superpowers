---
name: parallel-subagent-execution
description: Use when executing implementation plans with an Execution Graph — dispatches worktree-isolated agents per parallel group, merges as complete
---

# Parallel Subagent Execution

Execute an implementation plan by traversing its Execution Graph: dispatch all tasks in each parallel group concurrently, each in its own git worktree. Merge branches as they complete. Advance to the next group when all merges are done.

**Announce at start:** "I'm using the parallel-subagent-execution skill to implement this plan."

## When to Use

Use this skill when the implementation plan contains an **Execution Graph** section. If the plan has no Execution Graph, use `superpowers:subagent-driven-development` instead.

## Setup

1. Ensure you are on a feature branch worktree (use `superpowers:using-git-worktrees`). This is your merge target — implementer isolation is handled automatically per task via `isolation: "worktree"` on the Agent call.
2. Read the plan. If its header has a `**Design spec:**` field, read that file too — it is the source of truth for intent when a task's acceptance criteria are ambiguous.
3. Extract the Execution Graph from the plan
4. Compute topological layer order from dependency and parallel group annotations
5. Create a TodoWrite with all tasks

## Per Layer

For each parallel group (A, B, C… in topological order):

1. Dispatch all implementer agents for the group **concurrently** using `isolation: "worktree"` on each Agent call (single message, multiple Agent tool calls — see `./implementer-prompt.md`). After each agent completes, capture the returned `path` and `branch` from the Agent result — these are the isolated worktree the runtime created and the fresh branch committed to. Do not invent branch names.
2. As each implementer completes, run the two-stage review — dispatch reviewers **without** `isolation` and fill `WORKTREE_PATH` in the reviewer prompt with the implementer's returned `path` (do not wait for sibling tasks)
3. As each task passes both reviews, merge the implementer's returned `branch` into your feature branch:
   ```bash
   git merge <branch>  # <branch> is the value from the Agent result, not invented
   ```
   Before merging, sanity-check that `<branch>` is NOT your own feature branch name — if it is, isolation broke and you would be merging into yourself.
4. If merge produces a conflict, dispatch a conflict-resolution agent (see `./conflict-resolver-prompt.md`)
5. Once all tasks in the group are merged, remove worktrees using the returned paths:
   ```bash
   git worktree remove <path>
   ```
6. Advance to the next group

## Per Task: Implementer

Dispatch using `./implementer-prompt.md` with `isolation: "worktree"` on the Agent call. After the agent completes, capture the returned `path` and `branch`. Provide:
- Task metadata at the appropriate **detail tier** from the Execution Graph (contract, skeleton+intent, or acceptance-criteria)
- Acceptance criteria (always required)
- Scene-setting context (where this fits, what depends on it)
- The worktree path to work in: the `path` value returned by the Agent tool

The implementer produces:
- Implementation satisfying acceptance criteria
- Tests (agent decides when to write them; they must exist and pass before reporting DONE)
- A **rich commit message** explaining what was built, key design choices, and alternatives considered and rejected

## Per Task: Review

Both reviewers work in the task's existing worktree. Dispatch reviewers **without** `isolation: "worktree"` and fill `WORKTREE_PATH` in the reviewer prompt with the implementer's returned `path` — they need to read the implementer's worktree, not create a new one. Read the implementer's commit message via `git log` before reviewing.

**Stage 1 — Spec compliance** (`./spec-reviewer-prompt.md`):
- Did the agent build what was asked, nothing more, nothing less?
- Read actual code; do not trust the implementer's report
- Loop: implementer fixes → reviewer re-reviews → until ✅

**Stage 2 — Code quality** (`./code-quality-reviewer-prompt.md`):
- **Only dispatch after spec compliance passes ✅**
- Tests, maintainability, responsibility boundaries
- Loop: implementer fixes → reviewer re-reviews → until ✅

## Merge-as-Complete

As each task clears both reviews, merge immediately — do not wait for sibling tasks in the same group:

```bash
# From your main worktree (not the task worktree)
git merge <task-id>
```

**If the merge produces a conflict:** dispatch a conflict-resolution agent (`./conflict-resolver-prompt.md`) with both branch names, both commit messages, and the conflict diff. Do not resolve conflicts manually in your context — dispatch the agent.

Once the conflict agent commits a resolution, continue merging remaining branches.

## Model Selection

Each task's Execution Graph entry specifies a `**Model:**` field. Use it:

- `haiku` — dispatch with `model: "haiku"`
- `sonnet` — dispatch with `model: "sonnet"` (default if field is absent)
- `opus` — **requires user approval before dispatch.** Ask: "Task [id] is marked `opus` — approve?" Do not dispatch until approved.

Review agents (spec compliance, code quality) and conflict-resolution agents always use `sonnet`.

If the plan has no `**Model:**` field on a task, default to `sonnet`.

## Effort

Each task's Execution Graph entry specifies an `**Effort:**` field. If absent, default to `medium`. Pass the effort level to the implementer via the prompt (see `./implementer-prompt.md`) — do not infer or override it based on context.

## Handling Implementer Status

- **DONE:** Proceed to spec compliance review
- **DONE_WITH_CONCERNS:** Read concerns; address correctness issues before review; proceed if concerns are observations only
- **NEEDS_CONTEXT:** Provide missing context, re-dispatch
- **BLOCKED:** Assess blocker — provide context, upgrade model, break task, or escalate to human

## Red Flags

**Never:**
- Merge a branch before both review stages pass ✅
- Skip the conflict-resolution agent when `git merge` fails — do not resolve conflicts in your own context
- Advance to the next group before all current-group merges complete
- Make implementer subagents read the plan file — provide full task text in the prompt
- Skip scene-setting context
- Start on main/master without a feature branch worktree
- Manually create implementer worktrees with `git worktree add` — use `isolation: "worktree"` on the Agent call instead
- Use a hardcoded worktree path like `.worktrees/<task-id>` — use the `path` returned by the Agent tool
- Dispatch reviewers with `isolation: "worktree"` — they must read the implementer's existing worktree via `WORKTREE_PATH`, not create their own
- Merge a `<branch>` that matches your own feature branch name — that means isolation failed and the subagent committed to your branch; investigate before proceeding
- Dispatch an `opus` implementer agent without explicit user approval
- Use `opus` as a default when a task's model field is absent — default is `sonnet`
- Override the plan's `**Effort:**` field based on your own judgment — pass it through as-is

**If spec compliance review finds issues:** implementer fixes in the worktree, reviewer re-reviews. Do not merge until ✅.

**If code quality review finds issues:** implementer fixes, reviewer re-reviews. Do not merge until ✅.

## Integration

**Required workflow skills:**
- **superpowers:using-git-worktrees** — REQUIRED: Set up feature branch worktree as merge target before starting
- **superpowers:writing-plans** — Creates plans with Execution Graphs this skill executes
- **superpowers:finishing-a-development-branch** — Complete development after all tasks

**Prompt templates:**
- `./implementer-prompt.md` — Dispatch implementation subagent
- `./spec-reviewer-prompt.md` — Dispatch spec compliance reviewer
- `./code-quality-reviewer-prompt.md` — Dispatch code quality reviewer
- `./conflict-resolver-prompt.md` — Dispatch conflict resolution agent

**Alternative for sequential plans:**
- **superpowers:subagent-driven-development** — Use when plan has no Execution Graph
