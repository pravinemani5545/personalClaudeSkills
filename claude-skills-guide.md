# Claude Code Skills — Setup & Creation Guide

## What Are Skills?

Skills are custom slash commands for Claude Code. They're markdown files with a prompt that Claude follows when invoked. Think of them as reusable instruction sets.

---

## Where Skills Live

| Level | Path | Scope |
|-------|------|-------|
| **Personal** | `~/.claude/skills/<skill-name>/SKILL.md` | All your projects |
| **Project** | `.claude/skills/<skill-name>/SKILL.md` | Current project only |

**Legacy format** (still works): `~/.claude/commands/<name>.md` or `.claude/commands/<name>.md`

Skills take precedence over commands if both share the same name.

---

## Installing a Skill

1. Create the directory:
   ```bash
   mkdir -p ~/.claude/skills/my-skill
   ```

2. Add a `SKILL.md` file inside it:
   ```bash
   touch ~/.claude/skills/my-skill/SKILL.md
   ```

3. That's it — type `/my-skill` in Claude Code to use it.

Changes are picked up live. No restart needed.

---

## Skill File Structure

```
my-skill/
├── SKILL.md          # Required — the main prompt
├── reference.md      # Optional — loaded on demand
└── templates/        # Optional — supporting files
    └── example.ts
```

Keep `SKILL.md` under 500 lines. Move detailed reference material to supporting files.

---

## Frontmatter (YAML Header)

All fields are optional. Place between `---` markers at the top of `SKILL.md`.

```yaml
---
name: my-skill
description: What this skill does (Claude uses this to decide when to auto-invoke)
argument-hint: "[filename] [format]"
allowed-tools: Read Grep Bash(git *)
model: claude-opus-4-5
---
```

### Key Fields

| Field | Purpose |
|-------|---------|
| `name` | Slash command name. Defaults to directory name |
| `description` | Tells Claude when to use it. Front-load the key use case |
| `when_to_use` | Extra trigger phrases (appended to description) |
| `argument-hint` | Shown in `/` autocomplete menu |
| `arguments` | Named positional args (list or space-separated string) |
| `allowed-tools` | Tools Claude can use without approval during this skill |
| `model` | Override model for this skill's turn (e.g., `claude-opus-4-5`) |
| `effort` | Override effort level (`low`, `medium`, `high`, `xhigh`, `max`) |
| `context` | Set to `fork` to run in isolated subagent context |
| `agent` | Subagent type with `context: fork` (`Explore`, `Plan`, `general-purpose`) |
| `disable-model-invocation` | `true` = only user can invoke, Claude won't auto-trigger |
| `user-invocable` | `false` = hidden from `/` menu, only Claude can invoke |
| `paths` | Glob patterns to limit when Claude auto-activates |

---

## Arguments & Placeholders

The markdown body is the prompt. Use these placeholders for dynamic input:

| Placeholder | Description |
|-------------|-------------|
| `$ARGUMENTS` | Full argument string as typed |
| `$ARGUMENTS[0]`, `$ARGUMENTS[1]` | Positional (zero-indexed) |
| `$0`, `$1` | Shorthand for positional |
| `$name` | Named arg from `arguments` frontmatter |
| `${CLAUDE_SKILL_DIR}` | Path to the skill's directory |

**Example:** `/review auth.ts` → `$ARGUMENTS` = `auth.ts`, `$0` = `auth.ts`

---

## Shell Injection (Dynamic Context)

Run shell commands before the prompt is sent. Claude sees the output, not the command.

**Inline:**
```markdown
Current branch: !`git branch --show-current`
```

**Block:**
````markdown
```!
gh pr diff
```
````

---

## Invocation Control

| Config | User can invoke | Claude can invoke |
|--------|:-:|:-:|
| Default | Yes | Yes |
| `disable-model-invocation: true` | Yes | No |
| `user-invocable: false` | No | Yes |

---

## Minimal Example

`~/.claude/skills/greet/SKILL.md`:
```markdown
---
name: greet
description: Greet the user with a friendly message
---

Say hello to the user and ask what they'd like to work on today.
```

Invoke with: `/greet`

---

## Practical Example (with tools & arguments)

`~/.claude/skills/review-pr/SKILL.md`:
```markdown
---
name: review-pr
description: Review a GitHub pull request for code quality and issues
argument-hint: "[PR number]"
allowed-tools: Bash(gh *) Read Grep
model: claude-opus-4-5
---

Review PR #$ARGUMENTS for:
1. Code quality issues
2. Security concerns
3. Missing tests

PR diff: !`gh pr diff $ARGUMENTS`

Provide a summary with actionable feedback.
```

Invoke with: `/review-pr 42`

---

## Tips

- **Description matters most** — Claude reads it to decide when to auto-trigger
- **Keep SKILL.md focused** — offload detail to supporting files
- **Use `allowed-tools`** — avoids approval prompts for expected tool usage
- **Use `disable-model-invocation: true`** for destructive skills like `/deploy`
- **Test with `/` autocomplete** — verify your skill shows up and the hint looks right
- **Sharing:** commit `.claude/skills/` to version control for team-wide skills
