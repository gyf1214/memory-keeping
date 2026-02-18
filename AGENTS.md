# AGENTS.md

## Project Purpose
This project builds agent skills that memorize information across sessions, extract both project-specific and global knowledge, and improve process continuity over time.

## Scope
- Define the two-skill workflow in this repository:
  - `memory-keeping` for session-start and in-session memory updates.
  - `memory-consolidation` for end-of-task reconcile/condense/verification.
- Keep implementation process-oriented and iterative.
- Avoid premature decisions; refine behavior in later steps with the user.
- Self-improvement-loop mechanics and implicit skill telemetry are out of scope for the current version.

## Repository Layout
- `skills/memory-keeping/SKILL.md`: In-session memory capture/update skill definition.
- `skills/memory-consolidation/SKILL.md`: Task-boundary consolidation skill definition.
- `AGENTS.md`: Project guidance for agents working in this repository.
- `MEMORY.md`: Working memory for progress tracking and continuity.

## Available Skills
- `memory-keeping`: `skills/memory-keeping/SKILL.md`
- `memory-consolidation`: `skills/memory-consolidation/SKILL.md`

## Working Rules
- Always read `MEMORY.md` at the beginning of any task.
- Track progress and useful information in `MEMORY.md` while doing tasks and while interacting with the user.
- Always sync and condense `MEMORY.md` after finishing a task or at commit time.
- Always update `MEMORY.md` when instructed to remember something.
- `MEMORY.md` is intentionally git-ignored in this repository; do not force-add or commit it.
- When a commit is done by the agent, always include a co-author trailer for the agent in the commit message.

## MEMORY.md Purpose
`MEMORY.md` is the project's running memory for continuity across sessions. It records current stage, open questions, settled decisions, rules, notes, and planned next tasks.

## Editing Guidance
- Keep outlines and notes concise and factual.
- Prefer incremental updates over large rewrites.
- Do not treat placeholders as final decisions.
