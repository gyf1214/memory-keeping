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
- [Outline placeholder]

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
- [Outline placeholder]

## Workflow
- This section defines the ordered end-to-end process for reading context, extracting knowledge, writing memory, and condensing.
1. [Outline placeholder]
2. [Outline placeholder]
3. [Outline placeholder]

## Quick Reference
- This section provides a compact operator-facing checklist for common memory actions.
- [Outline placeholder]

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
- This section defines when and how memory files are created, modified, condensed, and corrected over time.
- File creation behavior:
  - If project `MEMORY.md` is missing, create it automatically.
  - If global `MEMORY.md` is missing, create it automatically.
  - When creating a new memory file, initialize a minimal skeleton.
- Minimal skeleton content:
  - `# MEMORY`
  - `## Rules`
  - The same minimal skeleton applies to both project and global memory files.
- Layer lifecycle behavior:
  - Do not pre-populate all layers at creation time because some layers may not apply.
  - Add layer sections incrementally only when they become relevant.
- Content maintenance behavior:
  - Keep rationale text in `decisions` entries only; keep other layers concise.
  - Hard constraints use explicit language such as `always` and `do not`.
  - Soft guidance uses explicit language such as `should` and `avoid`.
  - [Outline placeholder]

## Self-Improvement Loop
- This section defines how the skill improves itself from usage outcomes without drifting from core constraints.
- [Outline placeholder]

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
- [Outline placeholder]

## Common Mistakes
- This section lists frequent failure modes and the corrective action for each.
- [Outline placeholder]
