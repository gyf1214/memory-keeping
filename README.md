# Memory Keeping

Memory Keeping is an agent skill project for capturing, maintaining, and consolidating high-signal memory across sessions.

## Purpose
- Preserve durable project and global knowledge in structured memory files.
- Keep memory concise, current, and actionable.
- Improve continuity across tasks and sessions.

## Repository Structure
- `skills/memory-keeping/SKILL.md`: In-session memory capture/update workflow.
- `skills/memory-keeping/references/file-context.md`: File locations, boundaries, and context-loading details for memory capture.
- `skills/memory-keeping/references/memory-operations.md`: Data model and update rules for active work.
- `skills/memory-consolidation/SKILL.md`: End-of-task memory reconcile/condense workflow.
- `skills/memory-consolidation/references/file-context.md`: File locations and boundaries for consolidation.
- `skills/memory-consolidation/references/memory-operations.md`: Reconcile, condense, and verification rules.

## Installation
Install each skill subdirectory from `skills/` into your agent's skills directory; the exact path depends on the agent architecture.

For Codex, install to:
- `~/.agents/skills/memory-keeping`
- `~/.agents/skills/memory-consolidation`

### Recommended Installation: Symlink into the skills directory
- Clone this repository in any workspace location.
- Symlink both skill directories under `skills/` into your agent's skills directory.

Here's an end-to-end example for Codex:

```bash
# 1) Clone somewhere else
mkdir -p ~/src
cd ~/src
git clone https://github.com/gyf1214/memory-keeping.git memory-keeping

# 2) Link both skills into Codex skills directory
mkdir -p ~/.agents/skills
ln -s ~/src/memory-keeping/skills/memory-keeping ~/.agents/skills/memory-keeping
ln -s ~/src/memory-keeping/skills/memory-consolidation ~/.agents/skills/memory-consolidation

# 3) Verify
ls -la ~/.agents/skills/memory-keeping
ls -la ~/.agents/skills/memory-consolidation
```

## License
This project is licensed under the MIT License. See `LICENSE`.
