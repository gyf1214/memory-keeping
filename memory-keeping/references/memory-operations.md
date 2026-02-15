# Memory Operations Reference

This reference defines the detailed operating model for memory structure, extraction, updates, quality checks, and common failure modes.

## Data Model
- Buckets:
  - `project`
  - `global`
- Storage:
  - Markdown sections (no rigid per-entry schema).
  - File locations and path-resolution conventions are defined in `references/file-context.md` (`Files Involved`).
- Shared layers in each bucket:
  - `facts`: observed or verified state.
  - `decisions`: chosen direction with rationale.
  - `rules`: constraints and practices.
  - `open_questions`: unresolved items.
  - `plan`: intended next actions.
- Entry style:
  - Grouped notes are allowed; strict atomic-only entries are not required.

## Extraction Rules
- Capture only high-signal knowledge (facts, decisions, rules, open questions, plans).
- Do not use memory as an activity journal.
- `project` bucket: keep repository/workstream-specific knowledge that matters for future execution.
- `global` bucket: keep reusable cross-project patterns, heuristics, and constraints.
- Condense regularly to remove stale or redundant details while preserving durable signal.
- For design notes, meeting minutes, and plans:
  - Reference source files rather than mirroring full content.
  - Keep references current when source documents change.

## Update Rules
- Layer lifecycle:
  - Add layers incrementally when relevant; do not pre-populate all layers.
- Standard sequence:
  1. Classify into bucket (`project` or `global`) and layer (`facts`, `decisions`, `rules`, `open_questions`, `plan`).
  2. Check for existing overlapping meaning.
  3. Merge or replace instead of appending duplicates.
  4. For conflicts, keep only current best-known active state in `facts`; keep directional rationale in `decisions`.
  5. If uncertain and not verifiable in-session, write to `open_questions`.
  6. Wording policy for `rules`: hard constraints use `always`/`do not`; soft guidance uses `should`/`avoid`.
  7. Validate references and remove stale links.
- Scope transitions:
  - Promote `project` to `global` only when guidance is reusable across projects.
  - Rewrite promoted guidance to be context-agnostic and remove project-local specifics.

## Verification
- Verification is best effort (quality support), not a hard completion gate.
- Core checks when practical:
  - Correct bucket/layer placement.
  - No duplicate or contradictory active entries after reconcile.
  - Reference integrity (source still exists and supports stored summary).
  - Concise, high-signal entries (journal-style logs removed).
- If checks are skipped:
  - Record which checks were skipped and why.
  - Represent uncertainty as follow-up `open_questions`.
  - Prefer partial verified updates over unreviewed bulk additions.

## Common Mistakes
- Mistake: Logging activity steps.
  - Correction: Keep only durable high-signal knowledge.
- Mistake: Skipping end-of-task reconcile/condense.
  - Correction: Always reconcile at task boundary (including inferred boundaries such as `git commit`).
- Mistake: Appending duplicates.
  - Correction: Merge or replace overlapping entries.
- Mistake: Putting uncertainty in `facts`.
  - Correction: Use `open_questions` until verified.
- Mistake: Promoting project-local details to global memory.
  - Correction: Promote only reusable, context-agnostic guidance.
- Mistake: Leaving stale references.
  - Correction: Revalidate references during reconcile and remove invalid links.
- Mistake: Spreading rationale across layers.
  - Correction: Keep rationale in `decisions`.
- Mistake: Editing instruction files during memory updates.
  - Correction: Do not edit `AGENTS.md` or `LOCAL.md` unless explicitly instructed.
