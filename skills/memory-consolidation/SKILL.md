---
name: memory-consolidation
description: Use at explicit or inferred task boundaries to reconcile, condense, and verify project/global memory.
---

# Memory Consolidation Skill

## Overview
- This skill defines end-of-task memory maintenance.
- It reconciles and condenses high-signal memory across `project` and `global` buckets.
- It performs best-effort verification before task completion claims.

## When to Use
- At explicit task boundaries (for example user says task is done).
- At inferred boundaries (for example before/after `git commit`).
- After substantial work when memory may contain overlap, drift, or stale references.

## Relevant Files
- File inputs/outputs and edit boundaries are defined in `references/file-context.md`.
- Primary maintained files are project/global `MEMORY.md`.

## Workflow
1. Load context.
   - Read required instruction/memory files from `references/file-context.md`.
   - Read relevant source-of-truth project artifacts changed during the task.
2. Reconcile memory state.
   - Merge or replace overlapping entries.
   - Resolve active contradictions by keeping current best-known state in `facts` and rationale in `decisions`.
3. Condense memory.
   - Remove stale, redundant, and journal-like content.
   - Preserve durable high-signal facts, decisions, rules, open questions, and plan entries.
4. Verify and record uncertainty.
   - Apply verification checks from `references/memory-operations.md` (`Verification`).
   - If checks are skipped or uncertain, record that in `open_questions`.
5. Finalize task boundary.
   - Ensure both buckets are coherent and concise for next-session reuse.

## Quick Reference
- Trigger map:
  - Task boundary reached: run full consolidation pass.
  - Commit boundary reached: run consolidation before completion claims.
- If X, do Y:
  - If uncertain and not verifiable in-session, store in `open_questions`.
  - If entries overlap, merge or replace instead of appending.
  - If guidance is reusable across projects, promote to `global` in context-agnostic wording.

## Safety and Constraints
- Instruction precedence: apply `AGENTS.md` -> `LOCAL.md` -> `MEMORY.md`.
- Do not edit `AGENTS.md` or `LOCAL.md` unless explicitly instructed by the user.
- File loading and location details are defined in `references/file-context.md`.

## Verification
- Verification checks and fallback behavior are defined in `references/memory-operations.md` (`Verification`).

## Common Mistakes
- Common mistakes and corrective actions are defined in `references/memory-operations.md` (`Common Mistakes`).
