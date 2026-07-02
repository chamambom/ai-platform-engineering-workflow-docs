---
title: Token Efficiency & Cost Practices
nav_order: 8
---

# Token Efficiency & Cost Practices

Strategies and commands for preserving token usage, maintaining better context, and reducing costs when using Kiro CLI — particularly for codebase analysis and spec-driven feature design.

## Why This Matters

Every interaction consumes tokens. Long sessions, bloated context, and unnecessary tool schemas all add up. Being intentional about what goes into your context window directly impacts cost and quality of responses.

---

## 10 Best Practices

### 1. Use `/effort low` for Analysis and Exploration

For codebase reading, quick lookups, and questions that don't need deep reasoning, drop the effort level. Reserve `high` or `xhigh` for complex spec design work.

```
/effort low          ← fast answers, cheaper
/effort high         ← deep reasoning for spec design
```

---

### 2. Monitor and Manage Context with `/context`

Check your context usage regularly. Remove files you no longer need and add only what's relevant to the current task.

```
/context             ← see usage breakdown
/context add src/auth/**/*.ts
/context remove docs/**/*.md
```

---

### 3. Use `/compact` Before Hitting Context Limits

When a session gets long (especially during iterative analysis), compact it to preserve key findings while freeing space.

```
/compact
```

This creates an AI-generated summary of conversation history, replacing older messages with a condensed summary while preserving recent context.

---

### 4. Start Fresh Sessions Per Task with `/chat new`

Use spec mode to isolate tasks and keep them contained. Explicitly start new sessions for unrelated work — this prevents stale context from prior tasks bleeding through and consuming tokens.

```
/chat new analyse the payment module
```

---

### 5. Use `/knowledge` to Persist Analysis Results

Instead of re-analysing the same codebase every session, index it once and search semantically. Use "Fast" (BM25) for code, "Best" (semantic) for docs.

```
/knowledge add project-code ./src --include "**/*.ts" --exclude "node_modules/**"
/knowledge add project-docs ./docs
```

Then in future sessions, query against your knowledge base rather than re-reading files.

---

### 6. Create Focused Agents for Different Workflows

Build a read-only analysis agent (fewer tools = smaller tool definitions in context) and a separate feature-design agent. Less tool schema in context = more room for your code.

```json
// .kiro/agents/analyzer.json
{
  "name": "analyzer",
  "tools": ["read", "grep", "glob", "code"],
  "allowedTools": ["read", "grep", "glob", "code"],
  "resources": ["file://src/**/*.ts"]
}
```

Switch with: `/agent analyzer`

---

### 7. Enable Tool Search for MCP-Heavy Setups

If you have many MCP tools configured, Tool Search defers their schemas and only loads them on demand — saving significant context space.

```bash
kiro-cli settings toolSearch.enabled true
kiro-cli settings toolSearch.minPct 0
```

---

### 8. Use Steering Files Instead of Repeating Instructions

Put your recurring conventions in `.kiro/steering/` so they load automatically without you typing them every session. Keep them concise — every line costs context on every turn.

```
.kiro/steering/analysis-conventions.md
.kiro/steering/coding-standards.md
```

---

### 9. Use `/prompts` for Reusable Templates

Create prompt files in `.kiro/prompts/` for repeated tasks like "analyse module X" or "create spec for feature Y." Execute them with `@prompt-name` instead of typing out long instructions each time.

```
~/.kiro/prompts/analyse-module.md
@analyse-module src/payments
```

---

### 10. Delegate Parallel Work to Subagents

For large analysis tasks (e.g., auditing multiple modules), use subagents to fan-out the work. Each subagent gets its own context window, so you don't fill up your main session.

```
"Analyse the auth, payments, and notifications modules for security issues"
→ Kiro spawns 3 parallel subagents, each with clean context
```

---

## Quick Reference

| Goal | Command |
|------|---------|
| Check token usage | `/context` |
| Free up context space | `/compact` |
| Nuke and start clean | `/chat new` |
| Lower reasoning cost | `/effort low` |
| Persist codebase index | `/knowledge add` |
| Reusable task templates | `/prompts` or `@name` |
| Minimise tool bloat | `toolSearch.enabled true` |
| Persistent rules (no re-typing) | `.kiro/steering/*.md` |
| Scoped tool sets | `/agent analyzer` |
| Parallel analysis | subagents (ask naturally) |
| Check billing/credits | `/usage` |

---

## Biggest Wins for Codebase Analysis & Spec Design

- **Knowledge bases** — index once, query across sessions
- **Effort levels** — `low` for browsing, `high` for design
- **Focused agents** — read-only analyser vs. full-featured designer
- **Spec isolation** — one session per spec, fresh context each time
