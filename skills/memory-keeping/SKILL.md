---
name: memory-keeping
description: Use when starting a session, when asked to remember/correct/forget information, or when high-signal memory-worthy details emerge during work.
---

# Memory Keeping Skill

## Overview
- This skill defines how an agent captures and updates high-signal memory during active work.
- It uses two buckets (`project`, `global`) with shared layers for consistent updates and retrieval.
- End-of-task reconciliation and condensation are handled by `memory-consolidation`.

## When to Use
- Session start: load instruction and memory context before substantive work.
- In session: update memory when the user asks to remember/correct/forget, or when high-signal durable information emerges.
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
4. Handoff at task boundary.
   - When a task boundary is reached (for example `git commit`), invoke `memory-consolidation` for end-of-task memory maintenance.

## Quick Reference
- Trigger map:
  - Session start: load context and ensure memory files exist.
  - During work: capture high-signal updates; apply explicit remember/correct/forget requests immediately.
  - Task end: hand off to `memory-consolidation`.
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
