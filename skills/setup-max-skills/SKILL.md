---
name: setup-max-skills
description: "Apply Max's Claude Code configuration: git attribution off, ASD-STE100 output style, Remote Control at startup, and the mattpocock-skills plugin. Run it after installing, and again any time you want to change one of them."
disable-model-invocation: true
---

# Setup Max Skills

Apply Max's Claude Code configuration. Every setting below is user-scoped, so it belongs in `~/.claude/settings.json` and applies to all repos.

**On Codex or any agent that is not Claude Code, stop here.** Everything this skill writes is Claude Code configuration. Say so and do nothing.

Prompt-driven, not a script. Read the current state, show the diff, confirm, then write.

**Safe to run any number of times.** Every step converges on the same end state, and a re-run on an already-configured machine changes nothing. Re-run it to pick up a new setting, switch output style, or pull an upstream change.

## Process

### 1. Read the current state

- `~/.claude/settings.json`: the file this skill writes. Read it before you touch it. If it does not exist, create it.
- `.claude/settings.json` and `.claude/settings.local.json` in the current repo: project-local wins over user for both keys this skill cares about. `/config` writes `outputStyle` here, so a value here shadows the user-level style. A `remoteControlAtStartup: false` here turns auto-connect off for this repo alone. Report either, because neither is visible from `~/.claude/settings.json`.
- `~/.claude/output-styles/`: are `asd-ste100.md` and `i-have-adhd.md` already there, and do they still match this skill's copies? `diff` them.
- Installed plugins: is `mattpocock-skills` already there?

### 2. Present the diff and confirm

Show only what actually changes. A key that already holds the target value is a no-op: list it as already set, do not re-write it, and do not ask about it.

If nothing differs and both style files match, say the machine is already configured and skip to step 5. There is nothing to confirm.

Otherwise one confirmation for the whole set is enough.

A value that diverges deliberately is not a mistake to correct. If `attribution.commit` holds a custom trailer string rather than `false`, or `outputStyle` names a style that is not one of these two, point it out and ask before overwriting.

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

`outputStyle` is shown at its default here. On a re-run, keep the value that is already set unless the user asks to change it.

**`attribution`** turns off all three things Claude Code adds to git by default: the `Co-authored-by: Claude` commit trailer, the attribution line in PR descriptions, and the claude.ai session link in cloud and Remote Control commits. Max wants his commits to look like his commits.

If the file still has `includeCoAuthoredBy`, delete it. That key is deprecated and `attribution` replaces it.

**`outputStyle`** picks one of the two styles this skill ships.

Copy both files from [`./output-styles/`](./output-styles) into `~/.claude/output-styles/`. An identical file is a no-op. A file that differs is either an upgrade from this skill or a local edit the user made, and you cannot tell which apart: show the diff and ask before overwriting.

Then select one. Default to whatever `outputStyle` already holds, so a re-run never silently reverts an earlier choice; only fall back to `ASD-STE100` when the key is unset.

- [`ASD-STE100`](./output-styles/asd-ste100.md): Opus 5 writes dense prose by default. ASD-STE100 is a controlled language - short sentences, one idea each, plain approved words.
- [`i-have-adhd`](./output-styles/i-have-adhd.md): leads with the next action, numbers multi-step work, restates state across turns, kills tangents.

Both keep the coding instructions, so only the prose changes.

**`remoteControlAtStartup`** connects Remote Control at session start, in every repo. It is the same switch as **Enable remote control by default** in the desktop app and **Enable Remote Control for all sessions** in the VS Code extension.

Scope is asymmetric, which is the one way "always on" quietly stops being true:

- A `true` counts only from `~/.claude/settings.json` or managed settings. A checked-in project file cannot turn Remote Control on for everyone who clones the repo.
- A `false` counts from a project file too, and switches auto-connect off for that repo alone.

So a user-level `true` is necessary but not sufficient. If a repo does not auto-connect, look for a `false` in its `.claude/settings.json` or `.claude/settings.local.json`.

On Team and Enterprise plans, Remote Control is off until an Owner enables it in the [Claude Code admin settings](https://claude.ai/admin-settings/claude-code). No settings file overrides that.

### 4. Install mattpocock-skills

Matt Pocock's skills are in Claude Code's official marketplace, so there is no marketplace to add first:

```bash
claude plugin install mattpocock-skills
```

Skip this step if it is already installed.

### 5. Check the `i-have-adhd` upstream for updates

The [`i-have-adhd`](./output-styles/i-have-adhd.md) style is a vendored copy of a skill by [ayghri](https://github.com/ayghri/i-have-adhd) (MIT). It does not update itself. Check whether upstream has moved:

```bash
curl -sSL https://raw.githubusercontent.com/ayghri/i-have-adhd/main/skills/i-have-adhd/SKILL.md
```

Compare the body below the frontmatter against `./output-styles/i-have-adhd.md`. If it differs, show the diff and ask whether to pull the change in. Keep this skill's frontmatter either way; only the body is vendored. A pulled change belongs in both this skill's copy and `~/.claude/output-styles/i-have-adhd.md`.

### 6. Done

Tell the user what changed, or that nothing needed changing.

If `outputStyle` or `remoteControlAtStartup` changed, mention that both take effect at session start, so they need a restart or `/clear`. Skip that line when neither moved.
