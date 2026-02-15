# Memory Keeping

Memory Keeping is an agent skill project for capturing and maintaining high-signal memory across sessions.

## Purpose
- Preserve durable project and global knowledge in structured memory files.
- Keep memory concise, current, and actionable.
- Improve continuity across tasks and sessions.

## Repository Structure
- `memory-keeping/SKILL.md`: Operator-core skill definition.
- `memory-keeping/references/file-context.md`: File locations, boundaries, and context-loading details.
- `memory-keeping/references/memory-operations.md`: Data model, maintenance rules, verification, and common mistakes.

## Installation
Install the `memory-keeping/` subdirectory into your agent's skills directory; the exact path depends on the agent architecture.

For Codex, install `memory-keeping/` at `~/.agents/skills/memory-keeping`.

### Recommended Installation: Symlink into the skills directory
- Clone this repository in any workspace location.
- Symlink the `memory-keeping/` directory (not the repository root) into your agent's skills directory.

Here's an end-to-end example for Codex:

```bash
# 1) Clone somewhere else
mkdir -p ~/src
cd ~/src
git clone https://github.com/gyf1214/memory-keeping.git memory-keeping

# 2) Link into Codex skills directory
mkdir -p ~/.agents/skills
ln -s ~/src/memory-keeping/memory-keeping ~/.agents/skills/memory-keeping

# 3) Verify
ls -la ~/.agents/skills/memory-keeping
```

## License
This project is licensed under the MIT License. See `LICENSE`.
