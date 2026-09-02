---
title: Agent Context — Steering vs Skills
nav_order: 3
---

# Making the Agent Aware of Your Tools

A Kiro session only knows what's in its **context**. A helper script sourced in your
`~/.bashrc` is available to your shell, but the agent won't know it exists unless you
put that knowledge somewhere Kiro loads automatically.

There are two mechanisms. The choice comes down to **token cost vs. availability**.

## Steering files

Markdown files in `~/.kiro/steering/` (global) or `.kiro/steering/` (workspace). Loaded
into context **on every turn** — their full content is always present.

- **Good for:** rules that must shape *every* response (coding standards, PR conventions).
- **Cost:** the whole file sits in the context window on every turn, in every session.

> Token counting is approximate: **characters ÷ 4**. A ~550-character steering file is
> ~140 tokens — trivial on its own, but it's paid *every turn*. Keep steering files lean;
> move bulk reference material to a skill.

## Skills

A `SKILL.md` in `~/.kiro/skills/<name>/`. Only the **name + description** are always in
context; the full body loads **on demand** when the description matches the task.

- **Good for:** reference material used occasionally (e.g. "how to run a command across
  all accounts").
- **Cost:** near-zero when idle — just the description line.

```markdown
---
name: my-helper
description: What it does. Use when <trigger condition>.
---

# Body loads only when relevant
...instructions, functions, examples...
```

The **description is the trigger** — it must state *when* to use the skill, because that's
the only part always visible to the agent.

## Which to use

| | Steering file | Skill |
|---|---|---|
| Loaded | Full content, every turn | Metadata always; body on demand |
| Idle token cost | Whole file, every turn | Just name + description |
| Best for | Rules that shape every reply | Occasional reference / how-to |

**Rule of thumb:** if it must influence *every* response, use a lean steering file. If it's
"knowledge to reach for when a specific task comes up", use a skill.

## Example: a multi-account helper as a skill

I keep a Granted-based helper that logs into all my AWS accounts and runs commands across
them. Rather than pay for that in context on every turn, it's a skill — the description
tells the agent *when* to reach for it:

```markdown
---
name: aws-multi-account
description: Run AWS CLI commands across all accounts via the Granted helper. Use when a
  task spans multiple/all accounts at once, since the AWS MCP is one profile per session.
---

# AWS multi-account access
- myorg_login              — start the Granted SSO session
- myorg_profiles           — list all account profiles
- myorg_for_each <cmd>     — run a command against every account
```

When a single-account task comes up, the agent uses the AWS MCP. When a task fans out
across accounts, the description cues it to load this skill and use `myorg_for_each`.

> See [My AI Environment](MyEnvironment.md#working-across-many-accounts-with-granted) for
> the helper itself.
