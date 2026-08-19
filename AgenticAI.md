---
title: Agentic AI vs AI Workflows
nav_order: 2
---

# Agentic AI vs AI Workflows

They're related but not the same thing. Here's the distinction:

**AI Workflows** — deterministic, orchestrated pipelines where you define the steps upfront. The AI executes within a fixed flow. Think: "read ticket → generate spec → implement → push PR." You control the sequence, the AI does the work at each step. What I described in my ticket-to-PR workflow is an AI workflow — Kiro follows *my* instructions step by step.

**Agentic AI** — the AI decides *what to do next* autonomously. It plans, executes, observes results, and adapts. You give it a goal ("fix the production issue"), and it figures out the steps itself — maybe it checks logs, reads the code, identifies the bug, writes a fix, and creates a PR without you telling it each step.

**The spectrum:**

```
Simple Prompt → AI Workflow → Agentic AI
(one-shot)      (you orchestrate)   (it orchestrates itself)
```

Most of what I do daily sits in the **AI Workflow** space — and that's the right call for production infrastructure work where you want control and predictability. Agentic AI is where I'm exploring for the future (building agents that other platform teams can use).

---

## Why This Distinction Matters

Building an AI agent requires balancing autonomy, capability, and guardrails.

Key considerations include:

- Core architecture (planning, memory, and tools)
- Reliability (defining strict boundaries to prevent hallucinations)
- Observability (logging steps for auditing)
- Choosing the right level of autonomy (human-in-the-loop vs. fully autonomous)

Most enterprises now have at least one AI agent doing something useful — and honestly, that's the easy part. The hard part is what comes next: getting these agents to work reliably across teams, systems, and processes without falling over when the load goes up.

From what I've seen, the real challenge isn't building the agent — it's the infrastructure that sits beneath it. You need solid connectivity to your systems, proper governance and control, and orchestration that actually holds together as you scale. Without those foundations, you're just building something that works in a demo but crumbles in production.

This is the space I'm paying close attention to — how do we move from "I have an AI workflow" to "I have agents running reliably at enterprise scale"?

---

## My Attempt at Building One — Considerations

- Goal of the agent
- Guardrails — Authentication, Security
- Target Audience
