# Memory Keeping

Memory Keeping is an agent skill project for capturing, maintaining, and consolidating high-signal memory across sessions.

## Purpose
- Preserve durable project and global knowledge in structured memory files.
- Keep memory concise, current, and actionable.
- Improve continuity across tasks and sessions.

## Repository Structure
- `skills/memory-keeping/SKILL.md`: In-session memory capture/update workflow.
- `skills/memory-consolidation/SKILL.md`: End-of-task memory reconcile/condense workflow.

## Installation
Install this repository's `skills/` directory under your agent's skills directory.

For Codex, install to:
- `~/.agents/skills/memory-keeping`

Codex can discover skills in subdirectories, so this single install exposes both skills:
- `~/.agents/skills/memory-keeping/memory-keeping/SKILL.md`
- `~/.agents/skills/memory-keeping/memory-consolidation/SKILL.md`

### Recommended Installation: Symlink `skills/` as one namespace
- Clone this repository in any workspace location.
- Symlink the repository `skills/` directory to `~/.agents/skills/memory-keeping`.

Here's an end-to-end example for Codex:

```bash
# 1) Clone somewhere else
mkdir -p ~/src
cd ~/src
git clone https://github.com/gyf1214/memory-keeping.git memory-keeping

# 2) Link the whole skills namespace into Codex skills directory
mkdir -p ~/.agents/skills
ln -s ~/src/memory-keeping/skills ~/.agents/skills/memory-keeping

# 3) Verify
ls -la ~/.agents/skills/memory-keeping
ls -la ~/.agents/skills/memory-keeping/memory-keeping
ls -la ~/.agents/skills/memory-keeping/memory-consolidation
```

### Fallback: Link each skill separately
If your agent runtime does not discover nested skill directories, link each skill directly:

```bash
mkdir -p ~/.agents/skills
ln -s ~/src/memory-keeping/skills/memory-keeping ~/.agents/skills/memory-keeping
ln -s ~/src/memory-keeping/skills/memory-consolidation ~/.agents/skills/memory-consolidation
```

## License
This project is licensed under the MIT License. See `LICENSE`.
