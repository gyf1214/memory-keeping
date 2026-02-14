---
name: memory-keeping
description: Use when working on a process for memorizing and reusing project and global knowledge across sessions.
---

# Memory Keeping Skill

## Overview
- Maintain memory in two top-level buckets:
- `project`: information specific to the current repository/workstream.
- `global`: reusable cross-project knowledge.
- Use one shared internal layer schema in both buckets so extraction, updates, and retrieval stay consistent.

## When to Use
- [Outline placeholder]

## Inputs
- Instruction/context files to read:
- Global agent instructions: `AGENTS.md` in the agent home directory.
- Project agent instructions: `AGENTS.md` in the project directory.
- Project local-user instructions: `LOCAL.md` in the project directory (when present).
- Project memory: project `MEMORY.md`.
- Global memory: global `MEMORY.md`.

## Outputs
- [Outline placeholder]

## Workflow
1. [Outline placeholder]
2. [Outline placeholder]
3. [Outline placeholder]

## Quick Reference
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
- File creation behavior:
- If project `MEMORY.md` is missing, create it automatically.
- If global `MEMORY.md` is missing, create it automatically.
- When creating a new memory file, initialize a minimal skeleton.
- Do not pre-populate all layers at creation time because some layers may not apply.
- Add layer sections incrementally only when they become relevant.
- Minimal skeleton content:
- `# MEMORY`
- `## Rules`
- The same minimal skeleton applies to both project and global memory files.
- Shared internal layers (for each bucket):
- `facts`: observed or verified state.
- `decisions`: chosen direction with rationale.
- `rules`: constraints and practices; wording defines strictness.
- `open_questions`: unresolved items that require clarification or verification.
- `plan`: intended next actions and sequence.
- Entry format:
- Grouped notes are allowed; strict atomic-only items are not required.
- Rationale policy:
- Keep rationale text in `decisions` entries only; keep other layers concise.
- Rule wording convention:
- Hard constraints use explicit language such as `always` and `do not`.
- Soft guidance uses explicit language such as `should` and `avoid`.

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
- [Outline placeholder]

## Self-Improvement Loop
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
- [Outline placeholder]

## Common Mistakes
- [Outline placeholder]
