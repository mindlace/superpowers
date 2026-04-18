# Conflict Resolution Agent Prompt Template

Use this template when `git merge` fails during parallel layer execution.

```
Task tool (general-purpose):
  description: "Resolve merge conflict: [branch-a] vs [branch-b]"
  prompt: |
    A merge conflict occurred while integrating two parallel implementation branches.
    Your job is to resolve it correctly, preserving the intent of both implementations.

    ## What Happened

    These two branches were implemented in parallel as independent tasks in the same
    feature. Both implementations are intentional. Resolve the conflict in a way that
    preserves both implementations' intent — prefer merging approaches over discarding
    either side.

    ## Branch A: [branch-a]

    **What it implemented:** [task-a description from Execution Graph]

    Read the implementer's rationale:
    ```bash
    git log [branch-a] -1 --format="%B"
    ```

    ## Branch B: [branch-b]

    **What it implemented:** [task-b description from Execution Graph]

    Read the implementer's rationale:
    ```bash
    git log [branch-b] -1 --format="%B"
    ```

    ## The Conflict

    ```
    [paste full conflict diff — include file paths and all conflict markers]
    ```

    ## Your Job

    1. Read both commit messages to understand the intent behind each conflicting change
    2. Read the conflicting files in full in both branches to understand the full context
    3. Resolve the conflict preserving both implementations' intent where possible
    4. If both implementations made genuinely incompatible architectural choices, prefer
       the approach that better satisfies the overall feature goals — explain why in
       your commit message
    5. Run any available tests to verify the resolution does not break either task's
       acceptance criteria
    6. Commit with a message that explains: what conflicted, why, and how you resolved it

    ## When You Cannot Resolve

    If the conflict requires a design decision beyond your authority:
    - Do NOT guess
    - Report back with status BLOCKED
    - Describe specifically: what conflicted, what each branch intended, why they're
      incompatible, and what decision needs to be made by a human

    ## Report Format

    - **Status:** DONE | BLOCKED
    - What conflicted and why
    - How you resolved it (or what decision is needed, if BLOCKED)
    - Commit SHA of the resolution
```
