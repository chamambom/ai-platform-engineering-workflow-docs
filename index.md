---
title: Home
nav_order: 1
---

## The Backstory - My AI Adoption Journey

What's the most common mistake that we engineers make when building AI systems or workflows?

We jump straight to agents when a simple workflow would do the job better, faster, and cheaper. 🤦

- A single smart prompt. 
- A pipeline of LLM calls.
- A supervisor coordinating specialist agents. 
- A fully autonomous system.

Each architecture has its place. Knowing which fits your problem is a different skill entirely. 

... but how do you **build that intuition**

I've had the privilege of using AI tools within the Enterprise Circa -2024 when Gen AI made a big leap

- Kiro, 
- GitHub Copilot Cli, 
- GitHub Copilot Review

I've used them to 

- Understand codebases, 
- Resolve incidents, 
- Plan infrastructure work, and yes 
- Occasionally waste time using AI where it wasn't needed.

Here's what actually matters - 

- **Domain knowledge** - AI amplifies what you already know. In my case, that's Cloud Engineering.
- **Business context** - Knowing how to apply your skills to the business is the same with AI. Context is everything.

The takeaway? You're better placed to solve a problem with AI when you already understand the problem space. AI doesn't replace the thinking - it accelerates it.

As our team evolved, each of us adopted these tools to boost productivity in our own way. This repo documents that journey - capturing the detail that helps someone out there understand the tradeoffs.

## My Lens

As I use AI day to day, these are the questions I keep asking myself:

- **Cost** - What's the best way to track the cost of AI-assisted work?
- **Productivity** - How do we actually measure it?
- **Practices** - What does "best practice" look like when the tooling changes every month?
- **Security** - I connect AI to AWS, GitHub, and Terraform via MCP. How do I do that safely?
- **Scale** - When building an agent for other teams, what matters? Observability, auth, guardrails.

--- 

# AI-Enabled Engineering & Workflow Automation in the Workplace

Improved delivery velocity by embedding AI-assisted workflows using GitHub Copilot, Kiro, and GitHub CLI across infrastructure delivery and operational support.

## Workflows

| Workflow | Description |
|----------|-------------|
| [Codebase & Architecture Analysis](CodebaseAnalysis.md) | Analyse codebases to identify boundaries and support decoupling decisions |
| [Infrastructure Delivery](InfrastructureDelivery.md) | AI-assisted SDLC from Jira task through to PR merge |
| [Planning & Kanban Decomposition](PlanningWorkflow.md) | Identify dependencies and split tasks for delivery clarity |
| [Incident Management](IncidentManagement.md) | AI-assisted incident analysis, validation, and remediation |
| [Documentation & Knowledge Discovery](DocumentationDiscovery.md) | Retrieve operational knowledge to reduce onboarding time |
| [Agentic AI ](AgenticAI.md) | My approach towards building an AI agent. |

## Tools

- **GitHub Copilot** - code review, implementation assistance
- **Kiro** - design/spec generation, incident analysis, MCP context integration
- **GitHub CLI** - PR creation, branch management
- **MCP Tools** - Jira, Confluence, and Terraform repo context

---
## Patterns or best practices to save token costs



### Kiro
/usage - to check the credits used. 


### Github Copilot  



---