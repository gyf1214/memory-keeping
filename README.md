# Memory Keeping

Memory Keeping is an agent skill project for capturing and maintaining high-signal memory across sessions.

## Purpose
- Preserve durable project and global knowledge in structured memory files.
- Keep memory concise, current, and actionable.
- Improve continuity across tasks and sessions.

## Repository Structure
- `SKILL.md`: Operator-core skill definition.
- `references/file-context.md`: File locations, boundaries, and context-loading details.
- `references/memory-operations.md`: Data model, maintenance rules, verification, and common mistakes.

## Installation
This repository should be installed into the agent skills directory, which depends on the agent architecture.

For example, install into `~/.agents/skills/memory-keeping` for Codex.

### Option 1: Clone directly into the skills directory

End-to-end Codex example:

```bash
mkdir -p ~/.agents/skills
cd ~/.agents/skills
git clone https://github.com/gyf1214/memory-keeping.git memory-keeping
```

### Option 2: Symlink into the skills directory
- Clone this repository in any workspace location.
- Symlink it into your agent's skills directory.

End-to-end Codex example:

```bash
# 1) Clone somewhere else
mkdir -p ~/src
cd ~/src
git clone https://github.com/gyf1214/memory-keeping.git memory-keeping

# 2) Link into Codex skills directory
mkdir -p ~/.agents/skills
ln -s ~/src/memory-keeping ~/.agents/skills/memory-keeping

# 3) Verify
ls -la ~/.agents/skills/memory-keeping
```

## License
This project is licensed under the MIT License. See `LICENSE`.
