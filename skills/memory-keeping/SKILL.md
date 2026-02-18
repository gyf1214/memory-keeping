---
name: memory-keeping
description: Use at session start, when asked to remember information, and when high-signal information emerges during a session.
---

# Memory Keeping Skill

## Scope
- This skill is for loading memory and recording new memory during active work.
- It does not perform task-boundary consolidation.

## Data Model
- Buckets (scope): `project`, `global`
- Layers:
  - `project`: `facts`, `rules`, `open_questions`, `future_jobs`
  - `global`: `facts`, `rules`
- Entry importance:
  - Add `importance: low|medium|high|critical` when usefulness or recall priority should be explicit.

## File Context
- Global memory file: global `MEMORY.md`
- Project memory file: project `MEMORY.md`
- Precedence rule:
  - `system/developer -> global MEMORY.md -> project AGENTS.md -> project MEMORY.md`

## When To Use
- Beginning of a session
- User asks to remember information
- High-signal information appears during a session

## Workflow A: Load Memory At Session Start
1. Read global `MEMORY.md`.
2. Read project `AGENTS.md`.
3. Read project `MEMORY.md`.
4. Apply precedence: `system/developer -> global MEMORY.md -> project AGENTS.md -> project MEMORY.md`.

## Workflow B: Record During Session
1. Identify bucket: `project` or `global`.
2. Identify layer for that bucket.
3. Add or merge the entry (include `importance` when needed).

If bucket is unclear, ask:
- "Does this apply to all projects or only this project?"

## Extraction Rules
- Capture only high-signal durable memory.
- Keep entries concise and actionable.
- Avoid journaling activity logs, timestamps, or commit-level narration.

## Common Mistakes
- Forget to ask when bucket choice is unclear.
- Forget to merge overlapping entries.
- Forget to set `importance` when recall priority matters.
