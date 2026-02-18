---
name: memory-keeping
description: Use when starting a session, when asked to remember information, or when high-signal information emerges during a session.
---

# Memory Keeping Skill

## Scope
- This skill is for loading memory and recording new memory during active work.
- It does not perform task-boundary consolidation.

## Data Model
- Scopes: `project`, `global`
- Layers:
  - `project`: `facts`, `rules`, `decisions`, `open_questions`, `future_jobs`
  - `global`: `facts`, `rules`
- Entry importance:
  - `importance: low|medium|high|critical`.

## File Context
- Global memory file: global `MEMORY.md`
- Project memory file: project `MEMORY.md`
- Conflict resolution order (highest to lowest):
  - `system/developer -> chat -> project MEMORY.md -> project AGENTS.md -> global MEMORY.md`

## When To Use
- Beginning of a session
- User asks to remember information
- High-signal information appears during a session

## Workflow A: Load Memory At Session Start
1. Read global `MEMORY.md`.
2. Read project `AGENTS.md`.
3. Read project `MEMORY.md`.
4. Apply the conflict resolution order defined above.

## Workflow B: Record During Session
1. Identify scope: `project` or `global`.
2. Identify layer for that scope.
3. Add or merge the entry.

If scope is unclear, ask:
- "Does this apply to all projects or only this project?"

## Extraction Rules
- Capture only high-signal durable memory.
- Keep entries concise and actionable.
- Avoid journaling activity logs, timestamps, or commit-level narration.

## Common Mistakes
- Forget to read global or project memory.
- Forget to ask when scope choice is unclear.
- Forget to merge overlapping entries.
