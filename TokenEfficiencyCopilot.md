---
title: Token Efficiency - GitHub Copilot
nav_order: 9
---

# Token Efficiency & Cost Practices — GitHub Copilot

Strategies and commands for preserving tokens, maintaining better context, and reducing AI credit costs when using GitHub Copilot — particularly for codebase analysis and feature planning with Plan mode.

## Why This Matters

GitHub Copilot now uses usage-based billing. Every prompt, tool call, and context file sent counts against your AI credits. Cached tokens cost ~10% of fresh input tokens — so preserving the cache and keeping context lean directly impacts your bill.

---

## 10 Best Practices

### 1. Use Plan Mode for Feature Design (Research → Plan → Implement)

The biggest efficiency gain is separating thinking from doing. Use Plan mode to create a structured specification before writing code — then implement with a cheaper model.

**In Copilot CLI:**
```
/plan
```

**In VS Code:**
Select "Plan" from the agent dropdown, or type `plan` in the context window.

Plan with a strong reasoning model, then switch to a mid-tier or lighter model for implementation. This prevents expensive models from grinding through execution work.

---

### 2. Choose the Right Model for the Task

Model choice is one of the fastest ways to cut costs. Don't default to the most capable model for everything.

| Task Type | Model Tier | Examples |
|-----------|-----------|---------|
| Architecture, complex debugging | Reasoning | o3, Claude Sonnet |
| Execute a clear plan | Mid-tier | GPT-4o, Claude Haiku |
| Refactoring, docs, formatting | Lighter | GPT-4o-mini |

**Use Auto Model Selection** — it routes each request to the most efficient model and gives you a 10% discount on costs.

---

### 3. Start a New Conversation When You Switch Problems

A long thread carries its entire history as context into every new request. When you move to an unrelated task, start fresh.

**In Copilot CLI:**
```
/new
/clear
```

**In VS Code Chat:**
Click the `+` button to start a new chat session.

---

### 4. Compact Long Sessions with `/compact`

When you need the thread to keep going but it's grown large, compact it to summarise history and shrink the context window.

**In Copilot CLI:**
```
/compact
/compact focus on the auth module
```

You can optionally focus the summary on a specific topic so irrelevant context is dropped.

Use `/context` to check current usage at any time.

---

### 5. Preserve the Cache — Don't Switch Models Mid-Session

Cached tokens are billed at ~10% of normal input price. These actions **invalidate the cache** and force a full re-send:

- Switching models mid-session
- Coming back to a stale session (cache expires after 1-24 hours depending on model)
- Changing reasoning level, context size, or enabled tools mid-session

**Best practice:** Pick your model (or use auto model selection) at the start and stick with it for the session.

---

### 6. Set AI Credit Session Limits

Cap the amount of work Copilot performs on a single session to avoid runaway costs.

**In Copilot CLI:**
Set a session limit before initiating a task. When reached, the agent stops cleanly and lets you choose whether to continue.

Use this when:
- You want to cap credits on exploratory tasks
- You're tuning agent efficiency to find the minimum cost for a good result

---

### 7. Use `copilot-instructions.md` for Persistent Context

Instead of repeating project context every session, encode it once in a custom instructions file. This gives Copilot a structural overview so it doesn't waste tokens reading files to orient itself.

```
.github/copilot-instructions.md
```

**What to include:**
- Required frameworks, libraries, design patterns
- Known pitfalls the agent tends to repeat
- Build, test, and lint commands
- Team-specific conventions

**What to avoid:**
- Long generic documentation
- One-off preferences
- AI-generated guidance that doesn't reflect your actual system

---

### 8. Keep Context Lean — Only Bring What You Need

Copilot sends everything it has access to as input tokens: open editor tabs, attached files, full conversation history.

**Reduce context by:**
- Closing irrelevant editor tabs before chatting
- Using specific file references (`@file.ts`) rather than whole directories
- Enabling only the MCP toolsets relevant to the task
- Using `AGENTS.md` to provide a project map so the agent doesn't explore blindly

---

### 9. Use `/chronicle` to Learn from Your Own Usage

**In Copilot CLI:**
```
/chronicle tips          ← get efficiency suggestions from session history
/chronicle cost-tips     ← understand token usage patterns and reduce cost
```

Feed recurring insights into your `copilot-instructions.md` to turn one-time learnings into standing guidance.

---

### 10. Add Deterministic Guardrails (Tests, Linters, Scans)

Without guardrails, small agent errors compound — it builds on incorrect output, drifts further, and racks up token costs fixing mistakes.

Add:
- **Unit tests** — verify changes produced expected behaviour
- **Linters** — enforce structure, prevent formatting cleanup cycles
- **Security scans** — catch risky patterns early

This creates tight feedback loops. The agent makes a change, a test evaluates it, and it adjusts immediately rather than building chains of incorrect changes (one of the biggest drivers of token waste).

---

## Quick Reference

| Goal | Copilot Equivalent |
|------|-------------------|
| Spec/plan a feature | `/plan` or Plan agent in VS Code |
| Choose efficient model | Auto model selection (10% discount) |
| Start fresh per task | `/new` or `/clear` (CLI), new chat (VS Code) |
| Free up context space | `/compact` (optionally with focus topic) |
| Check context usage | `/context` |
| Persistent project instructions | `.github/copilot-instructions.md` |
| Cap session cost | AI credit session limits |
| Learn from usage patterns | `/chronicle tips`, `/chronicle cost-tips` |
| Reduce tool bloat | Configure MCP toolsets selectively |
| Monitor credits | `github.com/settings/billing` → AI usage |

---

## Kiro vs Copilot — Side-by-Side Comparison

| Concept | Kiro | GitHub Copilot |
|---------|------|----------------|
| Feature planning | Spec mode / `/plan` agent | Plan mode / Plan agent dropdown |
| Effort/reasoning level | `/effort low\|high\|max` | Configure reasoning level per model |
| Context monitoring | `/context` | `/context` |
| Compact history | `/compact` | `/compact` (with optional focus) |
| Fresh session | `/chat new` | `/new` or `/clear` |
| Persistent instructions | `.kiro/steering/*.md` | `.github/copilot-instructions.md` |
| Reusable prompts | `/prompts`, `.kiro/prompts/*.md` | Prompt files (`.github/prompts/*.md`) |
| Knowledge base | `/knowledge add` | Copilot Spaces / repository indexing |
| Parallel work | Subagents | Subagents (cheaper model routing) |
| Usage/billing check | `/usage` | `github.com/settings/billing` |
| Session insights | N/A | `/chronicle tips`, `/chronicle cost-tips` |
| Auto model routing | N/A (manual `/model`) | Auto model selection |

---

## Biggest Wins for Codebase Analysis & Feature Design

- **Plan mode** — always plan with a reasoning model, implement with a cheaper one
- **Auto model selection** — let Copilot route efficiently (+ 10% discount)
- **Cache preservation** — don't switch models mid-session; cached tokens are 90% cheaper
- **`copilot-instructions.md`** — encode project context once, stop repeating it
- **Session limits** — cap exploratory tasks so they don't spiral

---

## Further Reading

- [Optimizing your AI usage (GitHub Docs)](https://docs.github.com/en/copilot/tutorials/optimize-ai-usage)
- [Models and pricing](https://docs.github.com/en/copilot/reference/copilot-billing/models-and-pricing)
- [Context management in Copilot CLI](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/context-management)
