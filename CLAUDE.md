# Tentacles — Contributor Guidelines

Tentacles is a narrow, opinionated fork of [obra/superpowers](https://github.com/obra/superpowers). The guidelines below apply to this fork; do not submit tentacles changes upstream to superpowers (and vice versa — the projects have different goals).

## Scope

Tentacles is the **design → dependency-ordered plan → parallel execution** workflow, Claude Code only. Contributions must serve that workflow. Specifically:

- General-purpose skills that fit the main flow: yes.
- Sequential-execution variants, multi-harness adapters, third-party dependencies, domain-specific skills (portfolio, prediction markets, etc.): no — those belong in superpowers or a separate plugin.
- Changes to the upstream-inherited review-loop architecture, detail-tier system, or worktree-isolation design: acceptable, but require rationale and evidence.

## Pull request expectations

- **One problem per PR.** Bundled unrelated changes will be split or rejected.
- **State the problem.** Every PR describes a real issue someone experienced — a failing session, a confused agent, a concrete bug. Speculative or theoretical fixes ("my review agent flagged this") are not enough.
- **Show the diff to a human.** Agents may author changes, but a human must review the complete proposed diff before submission.

## Skill changes

Skills are code that shapes agent behavior. When modifying skill content:

- Run the change across multiple real sessions before submitting.
- Preserve carefully-tuned content (Red Flags tables, "your human partner" framing, rigid-skill discipline language) unless you have clear evidence the change improves outcomes.
- Prefer small, targeted edits over restructuring.

## Relationship to superpowers

Tentacles inherits most of its philosophy from superpowers. When in doubt about voice, terminology, or skill structure, follow superpowers' conventions — deliberate deviation is fine but should be intentional, not accidental.

Do not rebrand upstream content without replacing the underlying philosophy. "Superpowers with find-and-replace" is not the goal; a narrower tool with clearer scope is.
