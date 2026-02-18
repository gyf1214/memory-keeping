# File Context Reference

This reference defines the files used by this skill, how to treat each file, and where detailed context-loading/output constraints apply.

## Files Involved
Global files are in the agent home directory. Project files are in the project directory. Exact directory locations should come from agent runtime context.

- Global `AGENTS.md`:
  - Role: general instruction context for the agent runtime.
  - Use: read at consolidation time and apply as highest-level instruction source.
- Global `MEMORY.md`:
  - Role: cross-project memory store (`global` knowledge).
  - Use: reconcile and condense at task boundaries.
- Project `AGENTS.md`:
  - Role: project-specific instruction context.
  - Use: read at consolidation time and apply with higher specificity than global `AGENTS.md`.
- Project `LOCAL.md` (if present):
  - Role: local user/project refinement of instructions.
  - Use: read when present and apply with higher specificity than `AGENTS.md`.
- Project `MEMORY.md`:
  - Role: project bucket memory store (`project` knowledge).
  - Use: reconcile and condense at task boundaries.

Codex example:
- Global files are typically under `$CODEX_HOME` or `~/.codex`.
- Project files are typically in the current working directory or the git repository root.

## Context-Loading Rules
- Read instruction and memory files together before changing consolidated state.
- Use precedence order: `AGENTS.md` -> `LOCAL.md` -> `MEMORY.md`.
- Follow project-specific source-of-truth locations when defined.

## Output and Edit Boundaries
- Primary output targets are project/global `MEMORY.md`.
- Reference source artifacts instead of mirroring full content into memory.
- Keep references current when source artifacts change.
- Do not edit `AGENTS.md` or `LOCAL.md` unless explicitly instructed by the user.
- Keep project `LOCAL.md` and project `MEMORY.md` ignored by git (for example via project `.gitignore`).

## Missing-File Behavior
- If project or global `MEMORY.md` is missing, create:
  - `# MEMORY`
  - `## Rules`
- Use the same minimal skeleton for both project and global memory files.
- When creating a project `MEMORY.md`, ensure the project `.gitignore` includes both `MEMORY.md` and `LOCAL.md`.
