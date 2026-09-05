---
name: setup-max-skills
description: "Apply Max's Claude Code configuration: git attribution off, ASD-STE100 output style, Remote Control at startup, and the mattpocock-skills plugin. Run once after installing this plugin."
disable-model-invocation: true
---

# Setup Max Skills

Apply Max's Claude Code configuration. Every setting below is user-scoped, so it belongs in `~/.claude/settings.json` and applies to all repos.

Prompt-driven, not a script. Read the current state, show the diff, confirm, then write.

## Process

### 1. Read the current state

- `~/.claude/settings.json`: the file this skill writes. Read it before you touch it. If it does not exist, create it.
- `.claude/settings.local.json` in the current repo: `/config` writes `outputStyle` here, and project-local wins over user. If it sets `outputStyle`, say so, because the user-level style will not take effect until that key is removed.
- Installed plugins: is `mattpocock-skills` already there?

### 2. Present the diff and confirm

Show the exact JSON that will change. One confirmation for the whole set is enough.

### 3. Write `~/.claude/settings.json`

Merge these keys. Do not rewrite the file wholesale; keep every key that is already there.

```json
{
  "attribution": {
    "commit": false,
    "pr": false,
    "sessionUrl": false
  },
  "outputStyle": "ASD-STE100",
  "remoteControlAtStartup": true
}
```

**`attribution`** turns off all three things Claude Code adds to git by default: the `Co-authored-by: Claude` commit trailer, the attribution line in PR descriptions, and the claude.ai session link in cloud and Remote Control commits. Max wants his commits to look like his commits.

If the file still has `includeCoAuthoredBy`, delete it. That key is deprecated and `attribution` replaces it.

**`outputStyle`** picks one of the two styles this plugin ships. Ask which one; default to `ASD-STE100`.

- [`ASD-STE100`](../../output-styles/asd-ste100.md): Opus 5 writes dense prose by default. ASD-STE100 is a controlled language - short sentences, one idea each, plain approved words.
- [`i-have-adhd`](../../output-styles/i-have-adhd.md): leads with the next action, numbers multi-step work, restates state across turns, kills tangents.

Both keep the coding instructions, so only the prose changes.

**`remoteControlAtStartup`** connects Remote Control when a session starts, in every repo. This key is only honored from a user or managed file; a project file that sets `true` is ignored.

### 4. Install mattpocock-skills

Matt Pocock's skills are in Claude Code's official marketplace, so there is no marketplace to add first:

```bash
claude plugin install mattpocock-skills
```

Skip this step if it is already installed.

### 5. Check the `i-have-adhd` upstream for updates

The [`i-have-adhd`](../../output-styles/i-have-adhd.md) style is a vendored copy of a skill by [ayghri](https://github.com/ayghri/i-have-adhd) (MIT). It does not update itself. Check whether upstream has moved:

```bash
curl -sSL https://raw.githubusercontent.com/ayghri/i-have-adhd/main/skills/i-have-adhd/SKILL.md
```

Compare the body below the frontmatter against `output-styles/i-have-adhd.md`. If it differs, show the diff and ask whether to pull the change in. Keep this repo's frontmatter either way; only the body is vendored.

### 6. Done

Tell the user what changed. Two things need a restart or `/clear`:

- The output style is part of the system prompt, so it applies to the next session.
- `remoteControlAtStartup` acts at session start.
