# Tentacles Release Notes

## 0.1.0 — Initial tentacles release

Forked from [obra/superpowers](https://github.com/obra/superpowers) at v5.0.7.

### What tentacles is

A focused Claude Code plugin for **parallel subagent development**:

1. `tentacles:designing` — Socratic design refinement
2. `tentacles:writing-plans` — dependency-oriented Execution Graph
3. `tentacles:parallel-subagent-execution` — worktree-isolated implementer subagents dispatched per parallel group

### How it differs from superpowers

- **Dependency-oriented planning.** Plans ship with an Execution Graph that names parallel groups and per-task detail tiers (contract / skeleton+intent / acceptance-criteria), model, and effort.
- **Parallel subagent dispatch with worktree isolation.** Each implementer runs in its own git worktree created by the Agent runtime (`isolation: "worktree"`); merges happen per-task as reviews pass.
- **Tests required, not test-first.** Tasks must ship with passing automated tests, but the ordering of test vs. implementation is left to the implementer. See `tentacles:tests-required`.
- **More implementation freedom.** Detail tiers let planners specify only as much structure as an integration point needs; implementers decide the rest.

### Removed from the upstream fork

- `test-driven-development` — replaced by `tests-required`
- `systematic-debugging` — out of the main flow
- `writing-skills` — out of the main flow
- `subagent-driven-development` — sequential variant; tentacles only ships the parallel path
- `executing-plans` — already deprecated upstream
- Cursor / OpenCode / Codex / Gemini harness adapters — tentacles targets Claude Code only
- The deprecated `/brainstorm`, `/write-plan`, `/execute-plan` slash commands

### Renamed

- `brainstorming` → `designing`
- `using-superpowers` → `using-tentacles`
- Default spec/plan paths: `docs/superpowers/{specs,plans}/` → `docs/tentacles/{specs,plans}/`
- Worktree base: `~/.config/superpowers/worktrees/` → `~/.config/tentacles/worktrees/`
- Designing companion server: `TENTACLES_DESIGN_*` env vars; `.tentacles/designing/` session dir

### Credit

All core design, skill-authoring philosophy, and review-loop architecture originated in Jesse Vincent's [superpowers](https://github.com/obra/superpowers). Tentacles is a narrower, opinionated fork.

For the upstream release history see `RELEASE-NOTES-superpowers-history.md`.
