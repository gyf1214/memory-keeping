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
**Step1: Load Context:**
- Read global `MEMORY.md`, project `MEMORY.md`, and relevant design/index docs.

**Step2: Process Journal:**
- Process `Journal` top-to-bottom with one disposition per entry: move to `Knowledge`/`Rule`/`Work`/`Reference`, add a brief doc reference, or discard.
- For design behavior, architecture, or implementation mechanics, put detail in a design doc and keep only a short `Knowledge`/`Reference` pointer.
- For open questions: ask user when work allows. If confirmed, move result to the right section/doc. If undecided, add `Work`: `Decide on <topic>`.

**Step3: Normalize Persistent Memory:**
- Keep `Work` ordered (next action first), remove completed items, and keep design detail in docs with brief memory references.
- If `Knowledge` is >20 lines or >400 words, run a cleanup pass: move implementation-heavy items to design docs and replace with brief pointers.
- If still too large, add `Work`: `Create/refresh design doc for <topic cluster>` and suggest it to the user.

**Step4: Promote Reusable Memory:**
- Move (not copy) reusable items from reviewed `Journal` and project persistent entries to global `Knowledge`/`Rules`; if uncertain, ask before moving.

**Step5: Verify Exit Gates:**
- Confirm all `Journal` entries were reviewed, `Journal` is empty, and persisted memory is concise, current, and design/workflow relevant.

## Quick Reference
| If Journal entry is... | Do this |
|---|---|
| Durable project knowledge | Move to `Knowledge` |
| Design behavior / architecture / implementation detail | Put detail in design doc; keep only short pointer in `Knowledge`/`Reference` |
| Workflow constraint | Move to `Rule` |
| Open question | Ask user if current work allows; confirm -> move to section/doc; defer -> `Work`: `Decide on <topic>` |
| Duplicated, stale, or non-durable | Discard |

## Verification Rules
- Keep concise, durable, current memory only (no duplicates or conflicts).
- If a design or session document already holds detail, keep only a brief memory reference.
- Keep implementation-level behavior detail out of `Knowledge`; store it in design docs and retain only concise pointers in memory.
- Review ALL `Journal` entries (no skips) and exit with an empty `Journal`.
- Treat consolidation-time promotion as the normal path to global memory; allow direct global writes only on explicit user request.
- No histories, commit hashes, dates, timeline logs, or unnecessary settled rationale.

## Common Mistakes
- Forget to review docs/index context before editing memory.
- Forget to process `Journal` top-to-bottom with one disposition per entry.
- Forget to convert unresolved questions to `Work` and keep `Work` ordered.
- Forget to drain `Journal`.
