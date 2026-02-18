---
name: memory-consolidation
description: Use when a task boundary is reached and end-of-task memory maintenance is required.
---

# Memory Consolidation Skill

## Scope
- This skill runs at task boundaries only.
- It updates memory quality, resolves drift, and preserves only durable signal.

## Data Model
- Scopes: `project`, `global`
- Layers:
  - `project`: `facts`, `rules`, `decisions`, `open_questions`, `future_jobs`
  - `global`: `facts`, `rules`
- Entry importance:
  - `importance: low|medium|high|critical`

## File Context
- Global memory file: global `MEMORY.md`
- Project memory file: project `MEMORY.md`
- Conflict resolution order (highest to lowest):
  - `system/developer -> chat -> project MEMORY.md -> global MEMORY.md`

## When To Use
- At explicit task boundaries (for example, task done).
- At inferred boundaries (for example, before completion claims or commit finalization).

## Task-Boundary Workflow
1. If global `MEMORY.md` is missing, create it.
2. If project `MEMORY.md` is missing, create it and ensure `MEMORY.md` is in project `.gitignore`.
3. Review the session log for this task.
4. Identify documents created in this session (for example plans or notes) that already hold durable information.
5. Read global `MEMORY.md`.
6. Read project `MEMORY.md`.
7. Remove duplicate entries.
8. If memory content duplicates a session document, keep a brief reference to that document instead of mirroring the details in memory.
9. Shorten entries to concise, durable statements.
10. Remove outdated or conflicting entries.
11. Add missing high-signal information from the session log.
12. Find memory items the agent failed to recall during the session; add or raise `importance`.
13. Promote reusable items by moving (not copying) from project memory to global memory.
   - If uncertain whether promotion is appropriate, ask the user before moving it to global memory.
14. Keep only incomplete `future_jobs`; remove completed jobs while preserving key facts and decisions.
15. For autonomous choices made without user confirmation (for example work-alone choices), put issue, chosen action, and rationale in `open_questions` instead of settled facts or settled decisions.

## Verification Rules
- No duplication.
- No mirrored memory details when a session document already contains the same information; store a brief document reference instead.
- No outdated or conflicting active information.
- No journals, histories, commit hashes, dates, or timeline logs.
- Keep only concise durable memory.
- Avoid unnecessary rationale after a decision is settled.

## Common Mistakes
- Forget to review the session log before editing memory.
- Forget to read both global and project memory files.
- Forget to remove completed `future_jobs`.
- Forget to move reusable project memory into global memory.
- Forget to record autonomous unresolved choices in `open_questions`.
