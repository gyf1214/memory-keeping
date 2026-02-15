---
name: memory-keeping
description: Use when starting a session, when asked to remember/correct/forget information, when high-signal memory-worthy details emerge during work, or when completing a task.
---

# Memory Keeping Skill

## Overview
- This skill defines how an agent captures, stores, and reuses high-signal memory across sessions.
- It uses two buckets (`project`, `global`) with shared layers for consistent updates and retrieval.

## When to Use
- Session start: load instruction and memory context before substantive work.
- In session: update memory when the user asks to remember/correct/forget, or when high-signal durable information emerges.
- Task completion: reconcile and condense memory at task boundaries (can be inferred from workflow signals such as `git commit` unless project instructions define otherwise).
- Do not use this skill to keep a step-by-step activity journal.

## Relevant Files
- File inputs/outputs and edit boundaries are defined in `references/file-context.md`.
- Primary maintained files are project/global `MEMORY.md`.
- Project-specific non-memory file edits are only allowed when explicitly required by project instructions.

## Data Model
- Buckets: `project`, `global`.
- Layers: `facts`, `decisions`, `rules`, `open_questions`, `plan`.
- Explanations and storage conventions are defined in `references/memory-operations.md` (`Data Model`).

## Workflow
1. Load context.
   - Read required instruction/memory files from `references/file-context.md`.
   - Read relevant project source-of-truth artifacts for the current task.
2. Ensure memory files exist.
   - If project/global `MEMORY.md` is missing, create the minimal skeleton and apply ignore-file rules from `references/file-context.md` (`Missing-File Behavior`).
3. Update during work.
   - Apply extraction and update lifecycle rules from `references/memory-operations.md` (`Extraction Rules`, `Update Rules`).
   - Apply explicit remember/correct/forget requests immediately.
4. Reconcile and verify at task boundary.
   - Apply reconcile/verification guidance from `references/memory-operations.md` (`Verification`, `Common Mistakes`).
   - Task boundaries may be inferred from workflow signals such as `git commit` unless project rules define another boundary.

## Quick Reference
- Trigger map:
  - Session start: load context and ensure memory files exist.
  - During work: capture high-signal updates; apply explicit remember/correct/forget requests immediately.
  - Task end: reconcile both buckets, condense, and run best-effort verification.
- If X, do Y:
  - If information is uncertain, store it in `open_questions` (not `facts`).
  - If entry meanings overlap, merge or replace instead of appending.
  - If rationale is needed, store it in `decisions`.
  - If guidance is reusable across projects, consider promotion from `project` to `global`.

## Maintenance Rules
- Extraction and update lifecycle details are defined in `references/memory-operations.md` (`Extraction Rules`, `Update Rules`).

## Safety and Constraints
- Instruction precedence: apply `AGENTS.md` -> `LOCAL.md` -> `MEMORY.md`.
- Do not edit `AGENTS.md` or `LOCAL.md` unless explicitly instructed by the user.
- File loading, precedence interpretation examples, and location overrides are defined in `references/file-context.md`.

## Verification
- Verification checks and fallback behavior are defined in `references/memory-operations.md` (`Verification`).

## Common Mistakes
- Common mistakes and corrective actions are defined in `references/memory-operations.md` (`Common Mistakes`).
