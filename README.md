# personalClaudeSkills

My personal collection of [Claude Code](https://claude.com/claude-code) skills — a mix of ones I built from scratch and external ones I've adopted into my workflow. Each skill is a single markdown file that gets dropped into `~/.claude/skills/` and invoked as a slash command.

## What's in here

| Skill | Source | What it does |
|---|---|---|
| [`artifact`](artifact.md) | Built | Generates a self-contained HTML artifact (React + Tailwind, vanilla HTML/JS, SVG, or markdown), saves it to `~/Developer/artifacts/`, and auto-opens it in the browser. Closes the "artifacts gap" between Claude Code and the Claude app. |
| [`compress`](compress.md) | External (CPR bundle) | Prepares preservation notes before `/compact` and saves the full session to searchable logs. |
| [`preserve`](preserve.md) | External (CPR bundle) | Extracts the durable lessons from a session and writes them to `CLAUDE.md` so future conversations inherit them. |
| [`resume`](resume.md) | External (CPR bundle) | Loads context at the start of a session from `CLAUDE.md` + recent session logs. |
| [`claude-skills-guide`](claude-skills-guide.md) | Reference doc | Not a skill — a guide explaining how Claude Code skills work and how to create your own. |

> **Source legend** — *Built*: I wrote this skill. *External*: adopted from someone else's published work (lightly modified or used as-is). *Reference*: documentation, not a runnable skill.

## Install

Skills live in `~/.claude/skills/`. Claude Code picks them up automatically — no restart needed.

### Install everything

```bash
git clone https://github.com/pravinemani5545/personalClaudeSkills.git
cd personalClaudeSkills
mkdir -p ~/.claude/skills
cp *.md ~/.claude/skills/
```

### Install one skill

```bash
curl -o ~/.claude/skills/artifact.md \
  https://raw.githubusercontent.com/pravinemani5545/personalClaudeSkills/main/artifact.md
```

### Symlink instead (recommended for active development)

If you want edits in this repo to take effect immediately, symlink instead of copy:

```bash
mkdir -p ~/.claude/skills
for f in *.md; do
  [ "$f" = "README.md" ] && continue
  [ "$f" = "claude-skills-guide.md" ] && continue
  ln -sf "$(pwd)/$f" ~/.claude/skills/"$f"
done
```

## Use

Once installed, invoke a skill with a slash command:

```
/artifact pomodoro timer with start/pause
/compress
/preserve
/resume
```

You can also chain skills in one prompt — the first loads its context, then the next runs:

```
/frontend-design /artifact pricing page for a SaaS tool
```

Plain-language requests work too — skills auto-trigger based on intent (e.g. saying "build me an artifact of X" fires `/artifact`).

## Skill format

Each `.md` file is a self-contained skill. Frontmatter declares the metadata; the body is the instructions Claude follows when invoked.

```markdown
---
name: my-skill
description: One-line summary. Claude uses this to decide when to auto-trigger.
---

# My Skill

Instructions for Claude here...
```

See [`claude-skills-guide.md`](claude-skills-guide.md) for the full creation guide.

## License

Personal use. External skills retain their original authors' rights — see the individual files for attribution where applicable.
