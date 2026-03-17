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
1. If global `MEMORY.md` is missing, create it.
2. If project `MEMORY.md` is missing, create it and ensure `MEMORY.md` is in project `.gitignore`.
3. Review the session log for this task.
4. Identify design docs and other documents created or updated in this session that already hold durable information.
5. Read global `MEMORY.md`.
6. Read project `MEMORY.md`.
7. Review all `Journal` entries one by one; do not skip any entry.
8. For each `Journal` entry, do exactly one:
   - move to `Knowledge`, `Rule`, `Work`, or `Reference`;
   - update a relevant design doc or project index and keep only a brief reference in memory;
   - discard the entry if it is not durable or not important.
9. If an entry is an unresolved question, convert it to a `Work` item using the pattern `Decide on <topic>`.
10. Remove duplicate entries.
11. If memory content duplicates a design doc or session document, keep a brief reference to that document instead of mirroring the details in memory.
12. Update design docs when needed to keep canonical design detail there instead of in memory.
13. Shorten entries to concise, durable statements.
14. Remove outdated or conflicting entries.
15. Add missing high-signal information from the session log.
16. Find memory items the agent failed to recall during the session and add missing durable entries explicitly.
17. Promote reusable items by moving (not copying) from project memory to global memory.
   - If uncertain whether promotion is appropriate, ask the user before moving it to global memory.
18. Keep `Work` as an ordered queue where the top entry is the next actionable item; remove completed items while preserving key durable knowledge and rules.
19. Remove `Journal` entries only after careful review and explicit disposition.
20. Verify `Journal` is empty before exiting the skill.

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
- Forget to read both global and project memory files.
- Forget to convert unresolved questions into explicit `Work` items (`Decide on <topic>`).
- Forget to fully drain `Journal` before completion.
- Forget to move reusable project memory into global memory.
- Forget to keep design details in design docs and only references in memory.
