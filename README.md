# Tentacles

A Claude Code plugin for **parallel subagent development**: design → dependency-ordered plan → worktree-isolated parallel execution.

Named after the octopus — distributed brains acting in parallel, each tentacle capable of independent work while the whole remains coherent.

## Credit & relationship to superpowers

Tentacles is a fork of Jesse Vincent's [obra/superpowers](https://github.com/obra/superpowers). The skill-authoring philosophy, review-loop architecture, and most of the core skills originate there — tentacles would not exist without that foundation.

This fork exists because the parallel-execution path has diverged enough in intent to warrant its own identity:

| | superpowers | tentacles |
|---|---|---|
| Planning | flexible, multiple plan shapes | always a dependency-oriented Execution Graph |
| Execution | sequential (`subagent-driven-development`) + parallel options | parallel only (`parallel-subagent-execution`) |
| Tests | `test-driven-development` (RED-GREEN-REFACTOR) | `tests-required` (tests must exist and pass, but not test-first) |
| Scope | broad toolkit (debugging, skill-authoring, multiple harnesses) | narrow: design → plan → parallel execution, Claude Code only |
| Subagent autonomy | more prescriptive | more implementation freedom within per-task detail tiers |

If you want the broader toolkit, use superpowers. If you want parallel-only with more subagent autonomy, use tentacles. They are designed to coexist — different plugin names, different skill namespaces.

## The main flow

```
designing  →  writing-plans  →  parallel-subagent-execution
(Socratic    (dependency-      (dispatch implementer subagents
 refinement)  ordered plan      per parallel group in isolated
              with Execution    worktrees; merge per-task as
              Graph)            reviews pass)
```

Supporting skills: `using-git-worktrees`, `verification-before-completion`, `tests-required`, `requesting-code-review`, `receiving-code-review`, `finishing-a-development-branch`, `dispatching-parallel-agents`.

## Installation

Tentacles targets **Claude Code** only.

### From a local checkout

```bash
git clone <this-repo> ~/.claude/plugins/tentacles
```

Claude Code picks up plugins under `~/.claude/plugins/` automatically. Start a new session and the `SessionStart` hook will inject the `using-tentacles` bootstrap.

### Verifying installation

In a fresh Claude Code session, the `Skill` tool should list `tentacles:*` skills. If you see `superpowers:*` instead, you're running the upstream plugin.

## Skill catalogue

| Skill | Purpose |
|---|---|
| `using-tentacles` | Session-start bootstrap; skill discipline |
| `designing` | Socratic design refinement; writes spec to `docs/tentacles/specs/` |
| `writing-plans` | Dependency-oriented implementation plan with Execution Graph |
| `parallel-subagent-execution` | Dispatch worktree-isolated implementers per parallel group |
| `dispatching-parallel-agents` | General pattern for concurrent independent tasks |
| `using-git-worktrees` | Worktree setup for isolated feature branches |
| `verification-before-completion` | Evidence-based verification before success claims |
| `tests-required` | Tests must exist and pass, but not required to be written first |
| `requesting-code-review` | Dispatch reviewer subagents with focused context |
| `receiving-code-review` | Handle review feedback with rigor |
| `finishing-a-development-branch` | Merge / PR / cleanup after execution completes |

## Philosophy

- **Design before code.** Every project — even "simple" ones — goes through designing first.
- **Plans encode parallelism explicitly.** If two tasks are independent, the plan says so and they run concurrently.
- **Isolation is cheap.** Worktrees make parallel implementer dispatch safe; merge conflicts are an expected cost, handled by a conflict-resolution agent.
- **Tests exist, and pass, before completion.** Ordering is up to the implementer.
- **Implementers get the minimum prescriptive structure they need.** Detail tiers (contract / skeleton+intent / acceptance-criteria) calibrate that per-task.

## License

MIT. See `LICENSE` — upstream copyright (Jesse Vincent) is preserved, fork copyright added.
