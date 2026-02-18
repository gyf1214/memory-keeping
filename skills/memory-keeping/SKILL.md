---
name: memory-keeping
description: Use when starting a session, when asked to remember information, or when high-signal information emerges during a session.
---

# Memory Keeping Skill

## Scope
- Load memory and record durable memory during active work.
- Not for task-boundary consolidation.

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
1. Identify scope: `project` or `global`.
2. Identify layer for that scope.
3. Add or merge the entry.

If scope is unclear, ask:
- "Does this apply to all projects or only this project?"

## Extraction Rules
- Capture only high-signal durable memory.
- Keep entries concise and actionable.
- Avoid journals, timestamps, and commit narration.

## Common Mistakes
- Forget to read global memory first.
- Forget to ask when scope choice is unclear.
