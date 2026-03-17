---
name: memory-consolidation
description: Use when a task boundary is reached and end-of-task memory maintenance is required.
---

# Memory Consolidation Skill

## Scope
- This skill runs at task boundaries only.
- It updates memory quality, resolves drift, and preserves only durable signal.

## Data Model
- Scopes: `project`, `global`
- Layers:
  - `project`: `Knowledge`, `Rule`, `Work`, `Reference`, `Journal`
  - `global`: `Knowledge`, `Rules`

## Section Semantics
- `project/Knowledge`:
  - Durable and high-signal project memory only.
  - Keep only information that is really useful or important for a design point or workflow.
  - Do not store detailed implementation history, long design rationale, or narrative timelines.
- `project/Rule`:
  - Workflow constraints and operating rules only.
  - Do not store design decisions or implementation details here.
- `project/Work`:
  - Ordered queue of pending work; top item is next actionable work.
  - Use concise actionable entries.
  - Convert unresolved questions to `Decide on <topic>`.
- `project/Reference`:
  - Keep references only to the current project index and design docs relevant to the current project.
  - Do not duplicate design details already present in referenced documents.
- `project/Journal`:
  - Temporary intake for in-session notes, questions, and candidate memory.
  - Must be fully reviewed and drained during consolidation.
- `global/Knowledge`:
  - Reusable cross-project durable knowledge only.
- `global/Rules`:
  - Reusable cross-project workflow rules only.
  - Default source is consolidation-time promotion from project memory and reviewed `Journal` entries.

## File Context
- Global memory file: global `MEMORY.md`
- Project memory file: project `MEMORY.md`
- Conflict resolution order (highest to lowest):
  - `system/developer -> chat -> project MEMORY.md -> global MEMORY.md`

## When To Use
- At explicit task boundaries (for example, task done).
- At inferred boundaries (for example, before completion claims or commit finalization).

## Task-Boundary Workflow
1. Read global `MEMORY.md`, project `MEMORY.md`, the session log for this task, and relevant design docs or project index documents.
2. Process `Journal` entries top-to-bottom; review every entry and do exactly one disposition for each:
   - move to `Knowledge`, `Rule`, `Work`, or `Reference`;
   - if unresolved, convert to `Work` as `Decide on <topic>`;
   - update design docs or project index and keep only a brief memory reference;
   - discard if not durable, not important, or duplicates existing persistent memory or design docs.
3. Normalize persistent memory after Journal processing:
   - keep `Work` as an ordered queue with next actionable item at top, and remove completed items;
   - remove duplicates and conflicts;
   - shorten entries to concise durable statements;
   - if memory duplicates design docs, keep detail in docs and only brief references in memory.
4. Promote reusable items to global memory by moving (not copying) to global `Knowledge` or `Rules`, prioritizing reviewed `Journal` entries and including existing project persistent entries when needed.
   - If uncertain whether promotion is appropriate, ask the user before moving it to global memory.
5. Verify exit gates before finishing:
   - ALL `Journal` entries were reviewed;
   - `Journal` is empty;
   - persisted memory keeps only really useful and important design/workflow signal.

## Verification Rules
- No duplication.
- ONLY record information that is really useful and important for project design points or workflows.
- No mirrored memory details when a design doc or session document already contains the same information; store a brief document reference instead.
- No outdated or conflicting active information.
- Review ALL `Journal` entries; no skipped entries.
- `Journal` must be empty on skill exit.
- Treat consolidation promotion as the normal path to global memory; allow direct global writes only when explicitly requested by the user.
- No histories, commit hashes, dates, or timeline logs.
- Keep only concise durable memory.
- Avoid unnecessary rationale after a decision is settled.

## Common Mistakes
- Forget to review the session log before editing memory.
- Forget to process `Journal` top-to-bottom and handle every entry.
- Forget to convert unresolved questions into explicit `Work` items (`Decide on <topic>`).
- Forget to keep `Work` ordered with the next actionable item at the top.
- Forget to fully drain `Journal` before completion.
- Forget to move reusable project memory into global memory.
- Forget to keep design details in design docs and only references in memory.
