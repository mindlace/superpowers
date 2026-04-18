# Implementer Subagent Prompt Template

Use this template when dispatching an implementer subagent for a parallel task.

The `[DETAIL]` block varies by tier — paste the appropriate content from the task's Execution Graph entry.

```
Task tool (general-purpose):
  description: "Implement [task-id]: [task name]"
  model: "[haiku | sonnet | opus — from task's **Model:** field; default sonnet]"
  prompt: |
    You are implementing task [task-id]: [task name]

    ## Task Specification

    **Detail tier:** [contract | skeleton+intent | acceptance-criteria]
    **Effort level:** [low | medium | high — from task's **Effort:** field; default medium]

    Effort guidance:
    - `low` — follow the spec directly, minimal exploration, no extras
    - `medium` — standard depth: read related code for context, cover expected edge cases in tests
    - `high` — thorough: trace all call sites, cover edge cases exhaustively, validate assumptions explicitly

    [Paste ONE of the following blocks based on the task's detail tier:]

    ---
    IF tier = contract:
    **Contract — implement against this interface exactly. Do not change these signatures.**
    [paste contract from Execution Graph]

    **Acceptance criteria:**
    [paste acceptance criteria]
    ---

    ---
    IF tier = skeleton+intent:
    **Skeleton and intent:**
    [paste skeleton from Execution Graph]

    **Acceptance criteria:**
    [paste acceptance criteria]
    ---

    ---
    IF tier = acceptance-criteria:
    **Acceptance criteria:**
    [paste acceptance criteria]
    ---

    ## Context

    [Scene-setting: where this fits in the system, what depends on this task, what this task depends on, any architectural context needed to make good decisions]

    ## Before You Begin

    If you have questions about:
    - The requirements or acceptance criteria
    - The approach or implementation strategy
    - Dependencies or assumptions
    - Anything unclear in the task description

    **Ask them now.** Raise any concerns before starting work.

    ## Your Job

    Once you're clear on requirements:
    1. Implement what the spec requires
    2. Write tests satisfying the acceptance criteria — decide yourself when to write them, but they must exist and pass before you report DONE
    3. Verify all tests pass
    4. Write a **rich commit message** that includes:
       - What you built
       - Key design choices you made (and why)
       - Alternatives you considered and rejected (and why)
       - Anything a reviewer should know to evaluate your approach
    5. Commit
    6. Self-review (see below)
    7. Report back

    Work from: .worktrees/[task-id]

    **While you work:** If you encounter something unexpected or unclear, ask. Don't guess or make assumptions.

    ## Code Organization

    - Follow the file structure defined in the plan
    - Each file should have one clear responsibility with a well-defined interface
    - If a file is growing beyond the plan's intent, stop and report DONE_WITH_CONCERNS — don't split files on your own
    - Follow existing codebase patterns; improve what you touch but don't restructure outside your task

    ## When You're in Over Your Head

    It is always OK to stop and say "this is too hard for me." Bad work is worse than no work.

    **STOP and escalate when:**
    - The task requires architectural decisions with multiple valid approaches
    - You need to understand code beyond what was provided and can't find clarity
    - You feel uncertain about whether your approach is correct
    - You've been reading file after file without progress

    Report back with BLOCKED or NEEDS_CONTEXT. Describe specifically what you're stuck on.

    ## Before Reporting Back: Self-Review

    **Completeness:**
    - Did I fully implement everything in the spec?
    - Do all tests pass?
    - Did I miss any acceptance criteria?

    **Quality:**
    - Is this my best work?
    - Are names clear and accurate?
    - Is the code clean and maintainable?

    **Discipline:**
    - Did I avoid overbuilding (YAGNI)?
    - Did I only build what was requested?
    - Did I follow existing patterns?

    **Commit message:**
    - Does my commit message explain WHY I made each key choice?
    - Would a reviewer understand my intent from reading it?
    - Did I document alternatives I rejected?

    If you find issues during self-review, fix them before reporting.

    ## Report Format

    - **Status:** DONE | DONE_WITH_CONCERNS | BLOCKED | NEEDS_CONTEXT
    - What you implemented (or attempted, if blocked)
    - Test results (N passing)
    - Files changed
    - Commit SHA
    - Self-review findings (if any)
    - Concerns (if DONE_WITH_CONCERNS)
```
