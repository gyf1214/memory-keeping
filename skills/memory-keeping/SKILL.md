---
name: memory-keeping
description: Use when starting a session, when asked to remember information, or when high-signal information emerges during a session.
---

# Memory Keeping Skill

## Overview
This skill governs targeted memory lookup and captures candidate memory during active work.

**Core principle:** Initialize memory files, then search or record only when there is concrete durable signal.

## When To Use
- At session start.
- When the user asks to remember information.
- When high-signal durable memory appears during active work.
- When the user request, workspace context, error text, file path, tool name, or prior-context cue gives concrete memory-search keywords.

## File Context
- Global memory file: global `MEMORY.md`
- Project memory file: project `MEMORY.md`
- Project `Work`: ordered pending queue of unresolved/deferred actions; next action appears first.
- Conflict resolution order (highest to lowest):
  - `system/developer -> chat -> project MEMORY.md -> global MEMORY.md`

## Workflow A: Initialize And Search Memory
1. If global `MEMORY.md` is missing, create it.
2. If project `MEMORY.md` is missing, create it and ensure `MEMORY.md` is in project `.gitignore`.
3. If no concrete memory-search keywords are available, do not search memory and do not rely on it.
4. If keywords are available, make 2-3 focused `rg`/`grep` search attempts across project `MEMORY.md` and global `MEMORY.md`.
5. If searches find clear relevant entries, open only the matching sections or referenced docs needed for the task.
6. If searches find likely relevant entries but the context is unclear, load the full relevant memory file.
7. If searches find no relevant entries, stop memory lookup and continue normally without relying on memory.
8. Apply conflict order.
9. During execution, redo the search if repeated errors, confusing behavior, or likely prior context appears.

## Workflow B: Record During Session
1. Apply the signal gate: record only if a future agent would plausibly act better because of the entry.
2. Capture candidate memory relevant to project design/workflow in project `Journal` first.
3. Prefer evidence from user messages, user corrections, tool output, tests, logs, and files over assistant summaries.
4. Preserve short user wording when it makes a preference or rule more actionable.
5. Keep entries concise and actionable; avoid timestamps and commit narration.
6. Optional: include `scope_hint: project` or `scope_hint: global` when helpful.
7. Exception: if the user explicitly requests immediate global memory, update global `Knowledge` or `Rules` and add a brief `Journal` note for consolidation re-check.

## Common Mistakes
- Forget to create missing memory files before lookup.
- Fully reading memory before trying the focused `rg`/`grep` pass.
- Recording one-off status, temporary facts, or generic advice that will not change future behavior.
- Forget to capture memory into `Journal` first.
- Writing directly to global memory without explicit user request.
