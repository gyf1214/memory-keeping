---
name: memory-consolidation
description: Use at task boundaries to reconcile, clean, and verify project/global memory.
---

# Memory Consolidation Skill

## Scope
- This skill runs at task boundaries only.
- It updates memory quality, resolves drift, and preserves only durable signal.

## Data Model
- Buckets (scope): `project`, `global`
- Layers:
  - `project`: `facts`, `rules`, `open_questions`, `future_jobs`
  - `global`: `facts`, `rules`
- Entry importance:
  - Use or adjust `importance: low|medium|high|critical` when recall priority is underweighted.

## File Context
- Global memory file: global `MEMORY.md`
- Project memory file: project `MEMORY.md`
- Precedence rule:
  - `system/developer -> global MEMORY.md -> project AGENTS.md -> project MEMORY.md`

## When To Use
- At explicit task boundaries (for example, task done).
- At inferred boundaries (for example, before completion claims or commit finalization).

## Task-Boundary Workflow
1. Review the session log for this task.
2. Compare session log, project `MEMORY.md`, and global `MEMORY.md`.
3. Remove duplicates and outdated/conflicting entries.
4. Add missing high-signal information from the session log.
5. Find memory items the agent failed to recall during the session; add or raise `importance`.
6. Promote reusable items by move (not copy) from project memory to global memory.
7. Keep only incomplete `future_jobs`; remove completed jobs while preserving key facts.
8. For autonomous choices made without user confirmation (for example work-alone choices), put issue, chosen action, and rationale in `open_questions` instead of settled facts.

## Verification Rules
- No duplication.
- No outdated or conflicting active information.
- No journals, histories, commit hashes, dates, or timeline logs.
- Keep only concise durable memory.
- Avoid unnecessary rationale after a decision is settled.

## Common Mistakes
- Forget to review the session log before editing memory.
- Forget to remove completed `future_jobs`.
- Forget to move reusable project memory into global memory.
- Forget to record autonomous unresolved choices in `open_questions`.
