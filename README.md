# Max Skills

My personal agent skills, packaged as a Claude Code plugin.

## Installation

Add the marketplace, then install the plugin:

```
/plugin marketplace add OctronLab/max-skills
/plugin install max-skills@max
```

Or from the shell:

```bash
claude plugin marketplace add OctronLab/max-skills
claude plugin install max-skills@max
```

Then run `/setup-max-skills` once.

## Skills

- **[setup-max-skills](./skills/setup-max-skills/SKILL.md)**: apply my Claude Code configuration - git attribution off, ASD-STE100 or i-have-adhd output style, Remote Control at startup, and Matt Pocock's skills.
- **[teach-me](./skills/teach-me/SKILL.md)**: teach me a topic over multiple sessions, using the current directory as a stateful teaching workspace.

Both are user-invoked only (`disable-model-invocation: true`) - type the slash command to reach them.

## Layout

```
.claude-plugin/
  marketplace.json   # marketplace metadata, lists the plugin
  plugin.json        # plugin metadata, lists every skill path
skills/<name>/SKILL.md
output-styles/<name>.md
```

## Output styles

- **[ASD-STE100](./output-styles/asd-ste100.md)**: ASD-STE100 controlled language, to stop Opus writing dense prose. `setup-max-skills` selects it by default.
- **[i-have-adhd](./output-styles/i-have-adhd.md)**: leads with the next action, numbers multi-step work, restates state across turns. Vendored from [ayghri/i-have-adhd](https://github.com/ayghri/i-have-adhd) (MIT); `setup-max-skills` checks upstream for updates.

Adding a skill means creating `skills/<name>/SKILL.md` and adding its path to `plugin.json`.

## License

MIT
