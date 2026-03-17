---
name: memory-keeping
description: Use when starting a session, when asked to remember information, or when high-signal information emerges during a session.
---

# Memory Keeping Skill

## Scope
- Load memory and capture candidate memory during active work.
- Not for task-boundary consolidation.

## Data Model
- Scopes: `project`, `global`
- Layers:
  - `project`: `Knowledge`, `Rule`, `Work`, `Reference`, `Journal`
  - `global`: `Knowledge`, `Rules`
- `Work` ordering:
  - `Work` is an ordered queue; the top item is the next actionable work.
- Section meaning authority:
  - `memory-consolidation` defines canonical section semantics and inclusion/exclusion rules.
  - `memory-keeping` focuses on reading memory and capturing project updates into `Journal`.

## File Context
- Global memory file: global `MEMORY.md`
- Project memory file: project `MEMORY.md`
- Conflict resolution order (highest to lowest):
  - `system/developer -> chat -> project MEMORY.md -> global MEMORY.md`

## When To Use
- Session start
- Explicit remember request or new high-signal durable memory

## Workflow A: Load Memory At Session Start
1. If global `MEMORY.md` is missing, create it.
2. If project `MEMORY.md` is missing, create it and ensure `MEMORY.md` is in project `.gitignore`.
3. Read global `MEMORY.md` first.
4. Read project `MEMORY.md`.
5. Read project `AGENTS.md` only if present.
6. Apply conflict order.
7. If blocked or failing, re-read global then project memory before other debugging.

## Workflow B: Record During Session
1. Identify whether the candidate memory seems project-scoped or reusable across projects.
2. Write the candidate entry to project `Journal` first.
3. Defer persistent placement (`project/*` or `global/*`) to `memory-consolidation`.
4. Exception: if the user explicitly asks to remember something globally now, add or merge it in global `Knowledge` or `Rules` and also leave a brief `Journal` note so consolidation can re-check it.

If scope is unclear, ask:
- "Does this apply to all projects or only this project?"

## Extraction Rules
- Capture only information that is really useful and important for a project design point or workflow.
- Keep entries concise and actionable.
- During active work, use `Journal` as temporary capture and avoid direct writes to persistent project sections.
- Default to no direct global writes during active work; use consolidation promotion as the normal path.
- Avoid timestamps and commit narration.

## Common Mistakes
- Forget to read global memory first.
- Forget to ask when scope choice is unclear.
- Writing directly to global memory without explicit user request.
