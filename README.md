# personalClaudeSkills

My personal collection of [Claude Code](https://claude.com/claude-code) skills — a mix of ones I built from scratch and external ones I've adopted into my workflow. Each skill is a single markdown file that gets dropped into `~/.claude/skills/` and invoked as a slash command.

## Repo layout

Standalone skills sit at the repo root. **Bundles** — groups of related skills designed to work together — live in their own subfolder. Skills install flat into `~/.claude/skills/` regardless of where they live in the repo; the folder is purely for organization here.

```
.
├── artifact.md              # standalone skill
├── growth-sprint.md         # standalone skill
├── claude-skills-guide.md   # reference doc
└── cpr/                     # bundle: compress + preserve + resume
    ├── compress.md
    ├── preserve.md
    └── resume.md
```

## What's in here

| Skill | Bundle | Source | What it does |
|---|---|---|---|
| [`artifact`](artifact.md) | — | Built | Generates a self-contained HTML artifact (React + Tailwind, vanilla HTML/JS, SVG, or markdown), saves it to `~/Developer/artifacts/`, and auto-opens it in the browser. Closes the "artifacts gap" between Claude Code and the Claude app. |
| [`growth-sprint`](growth-sprint.md) | — | Built | Audits a project's current state (git history, strategy docs, tech stack, explicit exclusions) via an Explore agent, pauses for confirmation, then drafts a daily-execution sprint plan — 30/60/90 days — with 5 concrete tasks per day, 6-phase structure, standing daily habits, weekly cadence, and realistic outcomes. Saves to `~/Desktop/` and mirrors to Obsidian vault. |
| [`compress`](cpr/compress.md) | [`cpr`](cpr/) | External — [EliaAlberti/cpr](https://github.com/EliaAlberti/cpr-compress-preserve-resume) | Prepares preservation notes before `/compact` and saves the full session to searchable logs. |
| [`preserve`](cpr/preserve.md) | [`cpr`](cpr/) | External — [EliaAlberti/cpr](https://github.com/EliaAlberti/cpr-compress-preserve-resume) | Extracts the durable lessons from a session and writes them to `CLAUDE.md` so future conversations inherit them. |
| [`resume`](cpr/resume.md) | [`cpr`](cpr/) | External — [EliaAlberti/cpr](https://github.com/EliaAlberti/cpr-compress-preserve-resume) | Loads context at the start of a session from `CLAUDE.md` + recent session logs. |
| [`claude-skills-guide`](claude-skills-guide.md) | — | Reference doc | Not a skill — a guide explaining how Claude Code skills work and how to create your own. |

> **Source legend** — *Built*: I wrote this skill. *External*: adopted from someone else's published work (lightly modified or used as-is) — credit linked. *Reference*: documentation, not a runnable skill.

## Install

Skills live in `~/.claude/skills/`. Claude Code picks them up automatically — no restart needed.

### Install everything

Flattens all skills (root + bundles) into `~/.claude/skills/`, skipping the README and the reference guide:

```bash
git clone https://github.com/pravinemani5545/personalClaudeSkills.git
cd personalClaudeSkills
mkdir -p ~/.claude/skills
find . -name "*.md" \
  -not -name "README.md" \
  -not -name "claude-skills-guide.md" \
  -exec cp {} ~/.claude/skills/ \;
```

### Install a single bundle

```bash
cp cpr/*.md ~/.claude/skills/
```

### Install one skill

```bash
# standalone
curl -o ~/.claude/skills/artifact.md \
  https://raw.githubusercontent.com/pravinemani5545/personalClaudeSkills/main/artifact.md

# from a bundle
curl -o ~/.claude/skills/compress.md \
  https://raw.githubusercontent.com/pravinemani5545/personalClaudeSkills/main/cpr/compress.md
```

### Symlink instead (recommended for active development)

If you want edits in this repo to take effect immediately, symlink instead of copy:

```bash
mkdir -p ~/.claude/skills
find . -name "*.md" \
  -not -name "README.md" \
  -not -name "claude-skills-guide.md" \
  -exec sh -c 'ln -sf "$(pwd)/$1" ~/.claude/skills/"$(basename "$1")"' _ {} \;
```

## Use

Once installed, invoke a skill with a slash command:

```
/artifact pomodoro timer with start/pause
/growth-sprint aiopsforge 60
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
