# claude-skills

Skills for [Claude Code](https://claude.com/claude-code). Each directory is one skill, and the `SKILL.md` inside it is the entire skill.

## Skills

- [no-crap-docs](no-crap-docs/SKILL.md) — writing standards for code comments, docstrings, and docs
- [no-leaks](no-leaks/SKILL.md) — strips conversation and process residue out of code and docs

## Install

Clone the repository and symlink it as your personal skills directory:

```sh
git clone https://github.com/advaypakhale/claude-skills ~/projects/claude-skills
ln -s ~/projects/claude-skills ~/.claude/skills
```

If `~/.claude/skills` already has skills you want to keep, symlink single skills into it instead:

```sh
ln -s ~/projects/claude-skills/no-crap-docs ~/.claude/skills/no-crap-docs
```

Skills are picked up at the start of the next session.

## License

[MIT](LICENSE)
