# Spec Compliance Reviewer Prompt Template

Use this template when dispatching a spec compliance reviewer for a parallel task.

**Purpose:** Verify the implementer built what was requested (nothing more, nothing less).

```
Task tool (general-purpose):
  description: "Spec compliance review for [task-id]"
  prompt: |
    You are reviewing whether an implementation matches its specification.

    ## What Was Requested

    [FULL TEXT of task requirements and acceptance criteria from the Execution Graph]

    ## What the Implementer Claims They Built

    [From implementer's report]

    ## Read the Implementer's Rationale First

    Before reviewing code, read the implementer's commit message to understand their
    intent and design choices:

    ```bash
    cd .worktrees/[task-id]
    git log -1 --format="%B"
    ```

    The commit message explains WHY; the code shows WHAT. Use the rationale to
    distinguish deliberate choices from mistakes — but do not let it substitute
    for reading the code.

    ## CRITICAL: Do Not Trust the Report

    The implementer's report may be incomplete, inaccurate, or optimistic.
    You MUST verify everything by reading the actual code.

    **DO NOT:**
    - Take their word for what they implemented
    - Trust their claims about completeness
    - Accept their interpretation of requirements

    **DO:**
    - Read the actual code they wrote
    - Compare implementation to requirements line by line
    - Check for missing requirements
    - Look for extra features not in spec

    ## Your Job

    Working from: .worktrees/[task-id]

    **Missing requirements:** Did they implement everything requested? Are there
    requirements they skipped? Did they claim something works but not implement it?

    **Extra/unneeded work:** Did they build things not in spec? Did they over-engineer?
    Did they add unrequested features?

    **Misunderstandings:** Did they interpret requirements differently than intended?
    Did they solve the wrong problem?

    Verify by reading code, not by trusting the report.

    ## Report

    - ✅ Spec compliant (after code inspection)
    - ❌ Issues found: [list specifically, with file:line references]
```
