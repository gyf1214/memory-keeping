---
name: memory-keeping
description: Use when working on a process for memorizing and reusing project and global knowledge across sessions.
---

# Memory Keeping Skill

## Overview
- This skill defines how an agent captures, stores, and reuses high-signal memory across sessions.
- It uses two buckets (`project`, `global`) with a shared layer schema for consistent extraction, updates, and retrieval.

## When to Use
- This section describes concrete triggers and boundaries for when this skill should and should not be used.
- Use this skill at the beginning of a session.
  - Initialize context by loading instruction and memory sources before substantive task work.
- Use this skill when memory-relevant information appears during a session.
  - Trigger when the user asks to remember, correct, or forget information.
  - Trigger when the agent identifies high-signal information worth remembering.
- Use this skill when a task is completed.
  - Reconcile and condense memory at end-of-task boundaries.
  - End-of-task may be inferred from workflow signals such as `git commit` unless project instructions define a different boundary.
- Do not use this skill to maintain a step-by-step activity journal.

## Inputs
- Required instruction/context files:
  - Global agent instructions: `AGENTS.md` in the agent home directory.
  - Project agent instructions: `AGENTS.md` in the project directory.
  - Project local-user instructions: `LOCAL.md` in the project directory (when present).
  - Project memory: project `MEMORY.md`.
  - Global memory: global `MEMORY.md`.
- Runtime working inputs:
  - Current user instructions and conversation context.
  - Relevant project artifacts that may contain durable knowledge (for example design notes, plans, and meeting records).
  - Explicit user directives to remember, update, or remove memory entries.

## Outputs
- This section defines what memory updates, references, and persisted artifacts this skill produces.
- Primary outputs:
  - Updated project memory file: project `MEMORY.md`.
  - Updated global memory file: global `MEMORY.md`.
- Secondary outputs (project-dependent):
  - Updates to project-specific files only when project instructions explicitly require them.
  - Memory references to project source artifacts (for example design notes, plans, and meeting records) instead of mirrored copies.
- Non-target files by default:
  - Do not edit `AGENTS.md` or `LOCAL.md` unless explicitly instructed by the user.
  - If project-specific files must be edited, follow the project's file-location and editing rules.

## Workflow
- This section defines the ordered end-to-end process for reading context, extracting knowledge, writing memory, and condensing.
1. Load instruction context and source artifacts.
   - Read `AGENTS.md`, `LOCAL.md` (when present), project `MEMORY.md`, and global `MEMORY.md`.
   - Read any project-defined source-of-truth artifacts relevant to the current task (for example design notes, plans, and meeting records).
2. Ensure memory files exist.
   - If project or global `MEMORY.md` is missing, create it with the minimal skeleton:
   - `# MEMORY`
   - `## Rules`
3. Capture high-signal updates during work.
   - As new high-signal information becomes clear, apply incremental updates to the appropriate bucket/layer.
   - Avoid batching large unsorted updates.
4. Apply immediate-trigger updates.
   - If the user explicitly asks to remember, correct, or forget something, update memory immediately.
   - If source-of-truth documents change and invalidate memory entries, update or remove those entries in the same session when practical.
5. Reconcile and condense at end-of-task.
   - Reconcile both memory buckets against current state, merge duplicates, resolve contradictions, and remove stale content.
   - Validate references to project artifacts and remove or correct stale links.
   - End-of-task may be inferred from workflow signals such as `git commit` unless project instructions define a different boundary.
6. Run best-effort verification.
   - Run practical quality checks from `Verification`.
   - If checks are skipped, record the gap and represent unresolved uncertainty via `open_questions`.

## Quick Reference
- This section provides a compact operator-facing checklist for common memory actions.
- Trigger checklist:
  - Session start:
    - Load `AGENTS.md`, `LOCAL.md` (if present), project `MEMORY.md`, and global `MEMORY.md`.
    - Ensure project/global memory files exist; create minimal skeleton if missing.
  - During task:
    - Capture only high-signal items.
    - Apply immediate updates when user says remember/update/forget.
    - Update memory when source-of-truth changes invalidate stored entries.
  - Task end:
    - Reconcile both buckets, merge duplicates, resolve contradictions.
    - Condense entries and remove stale content/references.
    - Run best-effort verification and note skipped checks.
- If X, do Y:
  - If information is uncertain, write it to `open_questions` (not `facts`).
  - If entry meanings overlap, merge or replace instead of appending.
  - If a rule is hard, use `always`/`do not`; if soft, use `should`/`avoid`.
  - If rationale is needed, place it in `decisions`.
  - If guidance is reusable across projects, consider promotion from `project` to `global`.
  - If workflow signal indicates completion (for example `git commit`), run end-of-task reconcile.

## Data Model
- Top-level buckets:
  - `project`
  - `global`
- Storage format:
  - Memory is maintained in Markdown (no rigid record schema).
  - Each layer is represented as a section in the Markdown memory file.
- Storage locations:
  - `project` bucket is stored in `MEMORY.md` in the current project directory.
  - `global` bucket is stored in `MEMORY.md` in the agent home directory.
  - Agent should determine home and project directories from its runtime context.
  - Codex example (not a universal rule): global memory may resolve to `$CODEX_HOME/MEMORY.md` or `~/.codex/MEMORY.md`.
- Shared internal layers (for each bucket):
  - `facts`: observed or verified state.
  - `decisions`: chosen direction with rationale.
  - `rules`: constraints and practices; wording defines strictness.
  - `open_questions`: unresolved items that require clarification or verification.
  - `plan`: intended next actions and sequence.
- Entry format:
  - Grouped notes are allowed; strict atomic-only items are not required.

## Extraction Rules
- Default extraction policy:
  - Capture only high-signal knowledge (explicit facts, decisions, rules, open questions, and plans).
  - Avoid low-signal or ephemeral conversation details.
- Knowledge boundary:
  - Do not treat memory as a journal of agent activity.
  - Focus memory on what the agent learned, not a step-by-step log of what the agent did.
- Project-specific knowledge (`project` bucket):
  - Capture repository/workstream-specific facts, decisions, rules, open questions, and plans.
  - Exclude details that are irrelevant to future project decisions or execution.
- Global knowledge (`global` bucket):
  - Capture reusable cross-project patterns, heuristics, constraints, and lessons learned.
  - Exclude project-local details unless they generalize into reusable guidance.
- Condensing requirement:
  - Condense memory regularly because memory state changes over time.
  - During condensing, preserve high-signal learned knowledge and remove stale or redundant details.
- Relationship to design notes, meeting minutes, and plans:
  - Do not mirror full content of these documents into memory.
  - Store compact references to source files instead of duplicating large note content.
  - Reference format is intentionally free-form (no required template).
  - Keep memory consistent with source documents; remove or update stale memory entries immediately when source documents change.

## Update Rules
- This section defines how to modify memory entries when an update is performed in workflow steps 3-5.
- Layer lifecycle behavior:
  - Do not pre-populate all layers at creation time because some layers may not apply.
  - Add layer sections incrementally only when they become relevant.
- Standard update sequence:
  1. Classify the information into bucket (`project` or `global`) and layer (`facts`, `decisions`, `rules`, `open_questions`, `plan`).
  2. Check for an existing entry with overlapping meaning.
  3. Merge or replace instead of appending duplicates.
  4. If replaced, preserve durable rationale in `decisions` when relevant.
  5. Re-check wording strictness (`always`/`do not` for hard constraints; `should`/`avoid` for soft guidance).
  6. Validate references and remove stale links.
- Scope transition rules:
  - Promote from `project` to `global` only when guidance is reusable across projects.
  - When promoting, rewrite wording to be context-agnostic and remove project-local details.
  - Keep project-local examples in project memory only unless they are required as minimal evidence.
- Uncertainty handling:
  - If an item is uncertain and cannot be verified in-session, store it as an `open_questions` entry instead of a `facts` entry.
- Removal and supersession rules:
  - Remove entries that are stale, duplicated, contradicted, or no longer decision-relevant.
  - Do not preserve superseded entries as active memory; only preserve what remains operationally true.
  - If historical context matters, keep a concise rationale in `decisions` rather than parallel contradictory records.
- Conflict handling:
  - When new information conflicts with prior memory, preserve only the current best-known state in `facts` and move rationale for directional changes to `decisions`.
  - Do not keep contradictory active entries; resolve the conflict during reconcile.

## Safety and Constraints
- Instruction hierarchy and precedence:
  - `AGENTS.md` is most general instruction context.
  - `LOCAL.md` refines project-local user instructions.
  - `MEMORY.md` is least general and context-oriented.
  - Apply hierarchy as: `AGENTS.md` -> `LOCAL.md` -> `MEMORY.md`.
- Context loading requirement:
  - Always read relevant `AGENTS.md`, `LOCAL.md` (if present), and memory files together for a complete picture of instructions and context.
- Project-specific file-location rules:
  - If the project defines specific locations for design notes, meeting minutes, or plans, always follow those project rules first.
  - Generic storage conventions in this skill are fallback defaults only.

## Verification
- This section defines checks to confirm memory quality, consistency, and instruction compliance after updates.
- Verification mode:
  - Verification is best effort and supports self-improvement quality; it is not a hard completion gate.
- Core checks (run whenever practical):
  - Bucket/layer fit:
    - Each new or edited item is in the correct bucket (`project` or `global`) and layer (`facts`, `decisions`, `rules`, `open_questions`, `plan`).
  - Duplication and contradiction:
    - No duplicate entries with the same meaning remain after reconcile.
    - No active contradictions remain in the same bucket/layer.
  - Wording and rationale placement:
    - Hard constraints use `always`/`do not`; soft guidance uses `should`/`avoid`.
    - Rationale is kept in `decisions` rather than spread across other layers.
  - Reference integrity:
    - Referenced source files still exist and still support the stored summary.
  - Brevity and signal:
    - Entries remain concise and high-signal; journal-style logs are removed.
- Best-effort fallback when checks cannot all run:
  - Record which checks were skipped and why.
  - Mark uncertain outcomes as follow-up `open_questions` items.
  - Prefer partial verified updates over unreviewed bulk additions.

## Common Mistakes
- This section lists frequent failure modes and the corrective action for each.
- Mistake: Treating memory as an activity journal (logging every step).
  - Correction: Keep only high-signal facts, decisions, rules, open questions, and plans.
- Mistake: Forgetting to reconcile and condense at end-of-task.
  - Correction: Always run reconcile/condense at task boundary (including inferred boundaries such as `git commit`).
- Mistake: Appending duplicate entries instead of merging.
  - Correction: Check for overlapping meaning first, then merge or replace existing entries.
- Mistake: Putting uncertain claims into `facts`.
  - Correction: Store uncertainty in `open_questions` until verified.
- Mistake: Mixing project-local details into global memory.
  - Correction: Promote to `global` only when guidance is reusable across projects; keep local specifics in project memory.
- Mistake: Leaving stale references to source artifacts.
  - Correction: Re-check referenced files during reconcile; update or remove invalid links immediately.
- Mistake: Misplacing rationale across layers.
  - Correction: Keep rationale in `decisions`; keep other layers concise and operational.
- Mistake: Editing non-target instruction files during memory updates.
  - Correction: Do not edit `AGENTS.md` or `LOCAL.md` unless explicitly instructed by the user.
