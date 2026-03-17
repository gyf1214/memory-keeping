---
name: memory-keeping
description: Use when starting a session, when asked to remember information, or when high-signal information emerges during a session.
---

# Memory Keeping Skill

## Scope And Use
- Use at session start, explicit remember requests, or when high-signal durable memory appears during active work.
- This skill loads memory and captures candidate memory; it does not do task-boundary consolidation.

## File Context
- Global memory file: global `MEMORY.md`
- Project memory file: project `MEMORY.md`
- Conflict resolution order (highest to lowest):
  - `system/developer -> chat -> project MEMORY.md -> global MEMORY.md`
- Section semantics authority:
  - `memory-consolidation` defines canonical section inclusion/exclusion rules.

## Workflow A: Load Memory At Session Start
1. If global `MEMORY.md` is missing, create it.
2. If project `MEMORY.md` is missing, create it and ensure `MEMORY.md` is in project `.gitignore`.
3. Read global `MEMORY.md` first.
4. Read project `MEMORY.md`.
5. Read project `AGENTS.md` only if present.
6. Apply conflict order.

## Workflow B: Record During Session
1. Capture candidate memory relevant to project design/workflow in project `Journal` first.
2. Keep entries concise and actionable; avoid timestamps and commit narration.
3. Optional: include `scope_hint: project` or `scope_hint: global` when helpful.
4. Exception: if the user explicitly requests immediate global memory, update global `Knowledge` or `Rules` and add a brief `Journal` note for consolidation re-check.

## Common Mistakes
- Forget to read global memory first.
- Forget to capture memory into `Journal` first.
- Writing directly to global memory without explicit user request.
