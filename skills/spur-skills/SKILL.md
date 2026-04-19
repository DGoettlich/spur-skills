---
name: spur-skills
description: Use this skill when the user asks how the `spur-skills` tool works, or asks to install, update, or uninstall skills with `spur-skills`.
---

# spur-skills

Use this skill for questions about the functionalities or practical usage of the `spur-skills` tool itself.

## Guidelines

- If the user asks how `spur-skills` works, explain it in simple terms.
- If the user asks to install, update, or uninstall with `spur-skills`, use the matching command below.
- If the user wants to refresh installed skills without prompts, use `--yes`.
- If the user is working from a local checkout and changes do not show up, a fresh local reinstall may be needed.

## Installation

`spur-skills` is a `uv tool` maintained at https://github.com/DGoettlich/spur-skills. 

You can install it with:

```bash
uv tool install spur-skills
```

and uninstall it with:

```bash
uv tool uninstall spur-skills
```

## Commands

Install skills into all supported agent folders that already exist:

```bash
spur-skills install
```

Install and auto-accept overwrites:

```bash
spur-skills install --yes
```

Update the tool and then reinstall the skills:

```bash
spur-skills update
```

Update and auto-accept overwrites:

```bash
spur-skills update --yes
```

Remove installed skill entries from all supported agent folders and remove the shared skill folder:

```bash
spur-skills uninstall
```

Remove installed skill entries only from selected agents:

```bash
spur-skills uninstall codex claude
```

## How It Works

`spur-skills` installs skills in two steps.

1. It writes the full skill files to:

```text
~/.spur-skills/skills/
```

2. It writes small `SKILL.md` files containing only a pointer into agent skill folders such as:

```text
~/.codex/skills/
~/.claude/skills/
~/.hermes/skills/
~/.agents/skills/
~/.config/opencode/skills/
```

- The pointer's yaml headers are identical to the full skill to make sure the agent is triggered under the
same circumstances to use the skill.
- The pointers simply instruct the agent to read the shared copy of the skill in `~/.spur-skills/skills/`.
- The pointers also ask the agent to run the `update` command to make sure the skill is up to date.