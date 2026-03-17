---
name: memory-consolidation
description: Use when an explicit or implicit end-to-end execution boundary is reached after user feedback is addressed.
---

# Memory Consolidation Skill

## Scope
- Reconcile memory at task boundaries by removing drift and keeping durable signal.

## Memory Model
- Scopes: `project`, `global`
- Layers:
  - `project/Knowledge`: durable design/workflow signal.
  - `project/Rule`: workflow constraints and operating rules.
  - `project/Work`: ordered pending queue; unresolved questions become `Decide on <topic>`.
  - `project/Reference`: brief pointers to current project index/design docs.
  - `project/Journal`: temporary intake; every entry is reviewed and drained.
  - `global/Knowledge`: reusable cross-project knowledge.
  - `global/Rules`: reusable cross-project workflow rules.

## File Context
- Global memory file: global `MEMORY.md`
- Project memory file: project `MEMORY.md`
- Conflict resolution order (highest to lowest):
  - `system/developer -> chat -> project MEMORY.md -> global MEMORY.md`

## When To Use
- Trigger: an explicit or implicit end-to-end execution boundary after user feedback is addressed.
- Examples: finalized feature design doc; completed plan execution with user-addressed issues.

## Task-Boundary Workflow
1. Read global `MEMORY.md`, project `MEMORY.md`, and relevant design docs or project index documents.
2. Process `Journal` top-to-bottom and apply exactly one disposition per entry: move to `Knowledge`/`Rule`/`Work`/`Reference`, update docs with a brief memory reference, or discard non-durable/duplicate items.
   - For open-question entries: ask the user when the current work allows. If the user confirms direction, move the result to the related section and/or design doc. If no decision is made, add `Work` as `Decide on <topic>`.
3. Normalize persistent memory: keep `Work` ordered with next action at top, remove completed items, and keep design detail in docs with brief memory references.
4. Promote reusable items by moving (not copying) to global `Knowledge`/`Rules` from reviewed `Journal` entries and project persistent entries as needed.
   - If uncertain whether promotion is appropriate, ask the user before moving it to global memory.
5. Verify exit gates before finishing:
   - ALL `Journal` entries were reviewed;
   - `Journal` is empty;
   - persisted memory is concise, current, and design/workflow relevant.

## Quick Reference
| If Journal entry is... | Do this |
|---|---|
| Durable project knowledge | Move to `Knowledge` |
| Workflow constraint | Move to `Rule` |
| Open question | Ask user if current work allows; confirm -> move to section/doc; defer -> `Work`: `Decide on <topic>` |
| Duplicated, stale, or non-durable | Discard |

## Example
Journal: "Should uncertain promotion require user confirmation?"  
Ask user: confirm -> move policy to `Rule` (and global `Rules` if reusable); defer -> `Work`: `Decide on uncertain global-promotion default`.

## Verification Rules
- Keep concise, durable, current memory only (no duplicates or conflicts).
- If a design or session document already holds detail, keep only a brief memory reference.
- Review ALL `Journal` entries (no skips) and exit with an empty `Journal`.
- Treat consolidation-time promotion as the normal path to global memory; allow direct global writes only on explicit user request.
- No histories, commit hashes, dates, timeline logs, or unnecessary settled rationale.

## Common Mistakes
- Forget to review docs/index context before editing memory.
- Forget to process `Journal` top-to-bottom with one disposition per entry.
- Forget to convert unresolved questions to `Work` and keep `Work` ordered.
- Forget to drain `Journal`.
