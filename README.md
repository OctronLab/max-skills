# Max Skills

My personal agent skills. Built for Claude Code, installable on Codex too.

## Installation

### Any agent (recommended)

[skills.sh](https://skills.sh) copies the skill files into your project, and works with Claude Code, Codex, and anything else that reads `SKILL.md`:

```bash
npx skills@latest add OctronLab/max-skills
```

Pick the skills you want and which agents to install them on.

### Claude Code, as a plugin

A managed, read-only bundle that updates when I push:

```
/plugin marketplace add OctronLab/max-skills
/plugin install max-skills@max
```

Refresh later with `/plugin marketplace update max`.

Pick one route. Installing both leaves you with every skill twice.

Either way, run `/setup-max-skills` once afterwards.

## Skills

- **[setup-max-skills](./skills/setup-max-skills/SKILL.md)**: apply my Claude Code configuration - git attribution off, an output style, Remote Control at startup, the claude-powerline status line, Matt Pocock's skills, and herdr.
- **[teach-me](./skills/teach-me/SKILL.md)**: teach me a topic over multiple sessions, using the current directory as a stateful teaching workspace.

Both are user-invoked only (`disable-model-invocation: true`) - type the slash command to reach them.

## Output styles

Claude Code only; Codex has no equivalent. They ship inside `setup-max-skills`, which copies them to `~/.claude/output-styles/`, so they survive either install route.

- **[ASD-STE100](./skills/setup-max-skills/output-styles/asd-ste100.md)**: controlled language, to stop Opus writing dense prose. Selected by default.
- **[i-have-adhd](./skills/setup-max-skills/output-styles/i-have-adhd.md)**: leads with the next action, numbers multi-step work, restates state across turns. Vendored from [ayghri/i-have-adhd](https://github.com/ayghri/i-have-adhd) (MIT); `setup-max-skills` checks upstream for updates.

## Layout

```
.claude-plugin/
  marketplace.json   # marketplace metadata, lists the plugin
  plugin.json        # plugin metadata, lists every skill path
skills/<name>/
  SKILL.md
  agents/openai.yaml # Codex metadata: display name, implicit-invocation policy
```

A new skill means a `skills/<name>/SKILL.md`, an `agents/openai.yaml` beside it, and its path added to `plugin.json`. Anything a skill needs at runtime lives in its own folder, because that is all skills.sh copies.

## License

MIT
