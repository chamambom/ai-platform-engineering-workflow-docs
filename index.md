---
title: Home
nav_order: 1
---

# AI-Enabled Engineering & Workflow Automation

Improved delivery velocity by embedding AI-assisted workflows using GitHub Copilot, Kiro, and GitHub CLI across infrastructure delivery and operational support.

## Workflows

| Workflow | Description |
|----------|-------------|
| [Codebase & Architecture Analysis](CodebaseAnalysis.md) | Analyse codebases to identify boundaries and support decoupling decisions |
| [Infrastructure Delivery](InfrastructureDelivery.md) | AI-assisted SDLC from Jira task through to PR merge |
| [Planning & Kanban Decomposition](PlanningWorkflow.md) | Identify dependencies and split tasks for delivery clarity |
| [Incident Management](IncidentManagement.md) | AI-assisted incident analysis, validation, and remediation |
| [Documentation & Knowledge Discovery](DocumentationDiscovery.md) | Retrieve operational knowledge to reduce onboarding time |

## Tools

- **GitHub Copilot** - code review, implementation assistance
- **Kiro** - design/spec generation, incident analysis, MCP context integration
- **GitHub CLI** - PR creation, branch management
- **MCP Tools** - Jira, Confluence, and Terraform repo context

---

## The Backstory - My AI Adoption Journey

AI is just a tool. Used poorly, it's remarkably good at helping you:

- Overengineer simple tasks — building an agent when a reusable workflow would do.
- Rewrite things nobody asked for — crafting custom dashboards when free ones already exist.
- Sound confident while being completely wrong.

I've had the privilege of using AI tools daily — Kiro, GitHub Copilot, GitHub Copilot Review. I've used them to understand codebases, resolve incidents, plan infrastructure work, and yes — occasionally waste time using AI where it wasn't needed.

Here's what actually matters:

- **Domain knowledge** - AI amplifies what you already know. In my case, that's Cloud Engineering.
- **Business context** - Knowing how to apply your skills to the business is the same with AI. Context is everything.

The takeaway? You're better placed to solve a problem with AI when you already understand the problem space. AI doesn't replace the thinking - it accelerates it.

As our team evolved, each of us adopted these tools to boost productivity in our own way. This repo documents that journey - capturing the detail that helps someone out there understand the tradeoffs.

## My Lens

As I use AI day to day, these are the questions I keep coming back to:

- **Cost** - What's the best way to track the cost of AI-assisted work?
- **Productivity** - How do we actually measure it?
- **Practices** - What does "best practice" look like when the tooling changes every month?
- **Security** - I connect AI to AWS, GitHub, and Terraform via MCP. How do I do that safely?
- **Scale** - When building an agent for other teams, what matters? Observability, auth, guardrails.
