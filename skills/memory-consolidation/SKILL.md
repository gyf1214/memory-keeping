---
name: memory-consolidation
description: Use when an explicit or implicit end-to-end execution boundary is reached after user feedback is addressed.
---

# Memory Consolidation Skill

## Scope
- Runs at task boundaries only.
- Reconciles memory quality, resolves drift, and preserves durable signal.

## Memory Model
- Scopes: `project`, `global`
- Layers:
  - `project/Knowledge`: durable design/workflow signal; no implementation history or narrative timelines.
  - `project/Rule`: workflow constraints and operating rules only.
  - `project/Work`: ordered pending queue with next action at top; unresolved questions become `Decide on <topic>`.
  - `project/Reference`: brief pointers to current project index/design docs; keep details in docs.
  - `project/Journal`: temporary in-session intake; every entry must be reviewed and drained during consolidation.
  - `global/Knowledge`: reusable cross-project durable knowledge.
  - `global/Rules`: reusable cross-project workflow rules, usually promoted during consolidation.

## File Context
- Global memory file: global `MEMORY.md`
- Project memory file: project `MEMORY.md`
- Conflict resolution order (highest to lowest):
  - `system/developer -> chat -> project MEMORY.md -> global MEMORY.md`

## When To Use
- Trigger: an explicit or implicit end-to-end execution boundary after user feedback is addressed.
- Examples: feature design is finalized and documented; plan execution is finished and raised issues were addressed with the user.

## Task-Boundary Workflow
1. Read global `MEMORY.md`, project `MEMORY.md`, and relevant design docs or project index documents.
2. Process `Journal` top-to-bottom and apply exactly one disposition per entry: move to `Knowledge`/`Rule`/`Work`/`Reference`, update docs and keep only a brief memory reference, or discard non-durable/duplicate items.
   - For open-question entries: ask the user when the current work allows. If the user confirms direction, move the result to the related section and/or design doc. If no decision is made, add `Work` as `Decide on <topic>`.
3. Normalize persistent memory: keep `Work` ordered with next action at top and remove completed items; remove duplicates/conflicts; shorten entries; keep detailed design content in docs with only brief memory references.
4. Promote reusable items by moving (not copying) to global `Knowledge`/`Rules`, prioritizing reviewed `Journal` entries and using project persistent entries when needed.
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
| Open question | Ask user if current work allows; confirmed -> move to related section/doc, deferred -> `Work`: `Decide on <topic>` |
| Duplicated, stale, or non-durable | Discard |

## Example
Journal entry: "Should global promotion require user confirmation when uncertain?"  
Disposition: Ask the user first because promotion policy is uncertain.  
If user confirms, move the finalized policy into `Rule` (and to global `Rules` if reusable).  
If user defers, add `Work`: `Decide on uncertain global-promotion default`.

## Verification Rules
- No duplicates, conflicts, or outdated active information.
- Keep only concise durable memory that matters for project design/workflow.
- If a design or session document already holds detail, keep only a brief memory reference.
- Review ALL `Journal` entries (no skips) and exit with an empty `Journal`.
- Treat consolidation-time promotion as the normal path to global memory; allow direct global writes only on explicit user request.
- No histories, commit hashes, dates, timeline logs, or unnecessary settled rationale.

## Common Mistakes
- Forget to review docs/index context before editing memory.
- Forget to process `Journal` top-to-bottom with one disposition per entry.
- Forget to convert unresolved questions into `Work` (`Decide on <topic>`) and keep `Work` ordered.
- Forget to drain `Journal` or promote reusable items to global memory.
