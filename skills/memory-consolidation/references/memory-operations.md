# Memory Operations Reference

This reference defines the operating model for reconciling, condensing, and verifying memory at task boundaries.

## Data Model
- Buckets:
  - `project`
  - `global`
- Shared layers in each bucket:
  - `facts`
  - `decisions`
  - `rules`
  - `open_questions`
  - `plan`

## Reconcile Rules
- Merge or replace overlapping meaning; do not append duplicates.
- Keep only current best-known active state in `facts`.
- Keep rationale and directional tradeoffs in `decisions`.
- Move unresolved uncertainty to `open_questions`.
- Revalidate references and remove stale links.

## Condense Rules
- Remove journal-style activity logs.
- Remove stale or redundant entries while preserving durable signal.
- Keep wording concise and actionable.
- Promote to `global` only if guidance is reusable and context-agnostic.

## Verification
- Verification is best effort, but must be attempted before completion claims.
- Core checks:
  - Correct bucket/layer placement.
  - No duplicate or contradictory active entries after reconcile.
  - Reference integrity.
  - Concise high-signal content after condense.
- If checks are skipped:
  - Record skipped checks and why.
  - Record unresolved uncertainty in `open_questions`.

## Common Mistakes
- Mistake: Treating consolidation as optional at task boundaries.
  - Correction: Always run consolidation at explicit or inferred boundaries.
- Mistake: Appending duplicate entries.
  - Correction: Merge or replace overlapping entries.
- Mistake: Keeping uncertainty in `facts`.
  - Correction: Move uncertainty to `open_questions`.
- Mistake: Preserving stale references.
  - Correction: Revalidate or remove stale references.
- Mistake: Promoting project-specific notes to `global`.
  - Correction: Promote only reusable, context-agnostic guidance.
