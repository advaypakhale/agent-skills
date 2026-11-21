# agent-skills

Skills in the Agent Skills format: each directory is one skill, and the `SKILL.md` inside it is the entire skill.

## Skills

- [cad](cad/SKILL.md) — modular, parametric CadQuery modeling and STEP reverse engineering
- [no-crap-docs](no-crap-docs/SKILL.md) — writing standards for code comments, docstrings, and docs
- [no-leaks](no-leaks/SKILL.md) — strips conversation and process residue out of code and docs

## Install

For Claude Code, clone the repository and symlink it as your personal skills directory:

```sh
git clone https://github.com/advaypakhale/agent-skills ~/projects/agent-skills
ln -s ~/projects/agent-skills ~/.claude/skills
```

If `~/.claude/skills` already has skills you want to keep, symlink single skills into it instead:

```sh
ln -s ~/projects/agent-skills/no-crap-docs ~/.claude/skills/no-crap-docs
```

Skills are picked up at the start of the next session. Other agents that support the format read the same directories; see your agent's documentation for where it looks.

## License

[MIT](LICENSE)
