# File Context Reference

This reference defines the files used by this skill, how to treat each file, and where detailed context-loading/output constraints apply.

## Files Involved
Global files are in the agent home directory. Project files are in the project directory. Exact directory locations should come from agent runtime context.

- Global `AGENTS.md`:
  - Role: general instruction context for the agent runtime.
  - Use: read at session start and apply as highest-level instruction source.
- Global `MEMORY.md`:
  - Role: cross-project memory store (`global` knowledge).
  - Use: read at session start; update when guidance is reusable across projects.
- Project `AGENTS.md`:
  - Role: project-specific instruction context.
  - Use: read at session start and apply with higher specificity than global `AGENTS.md`.
- Project `LOCAL.md` (if present):
  - Role: local user/project refinement of instructions.
  - Use: read when present and apply with higher specificity than `AGENTS.md`.
- Project `MEMORY.md`:
  - Role: project bucket memory store (`project` knowledge).
  - Use: read at session start; update during explicit/implied triggers.

Codex example:
- Global files are typically under `$CODEX_HOME` or `~/.codex`.
- Project files are typically in the current working directory or the git repository root.

## Context-Loading Rules
- Read relevant instruction and memory files together before relying on stored memory state.
- Use precedence order: `AGENTS.md` -> `LOCAL.md` -> `MEMORY.md`.
- A project `AGENTS.md` may mirror these file definitions or provide variants. When in conflict, follow the project-specific instructions.
- If project instructions define source-of-truth locations (for example design notes, meeting minutes, plans), follow those project rules over generic defaults in this skill.

## Output and Edit Boundaries
- Primary output targets are project/global `MEMORY.md`.
- Reference source artifacts instead of mirroring full content into memory.
- Keep references current when source artifacts change.
- Do not edit `AGENTS.md` or `LOCAL.md` unless explicitly instructed by the user.
- Edit project-specific non-memory files only when project instructions explicitly require those edits.
- Keep project `LOCAL.md` and project `MEMORY.md` ignored by git (for example via project `.gitignore`).

## Missing-File Behavior
- If project or global `MEMORY.md` is missing, create:
  - `# MEMORY`
  - `## Rules`
- Use the same minimal skeleton for both project and global memory files.
- When creating a project `MEMORY.md`, ensure the project `.gitignore` includes both `MEMORY.md` and `LOCAL.md`.
