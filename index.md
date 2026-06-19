---
title: Home
nav_order: 1
---

# My AI Adoption Journey — Strategy & Tips

## The Backstory

It's already been 15 years and I've worked extensively as a Cloud Specialist (2010 – to date). Along the way, I've touched Python, MongoDB, Linux, VMware, Cisco, Dell, AWS, Azure — you name it — and validated my knowledge through certifications like RHCE, Azure & AWS Solutions Architect. But somewhere along the line, I got comfortable, and I realised I wanted to reset my curiosity and re-learn *how to learn*.

So around Covid, I decided to enrol for an MSc. I already had the Cloud experience, which meant I needed something that would *complement* what I knew rather than repeat it. In my mind, degrees in AI, Cybersecurity, or Cloud Computing get outdated too fast — industry moves quicker than any curriculum can keep up. That led me to **Data Engineering** — a mature field with solid tooling like Databricks, Azure Data Factory, and AWS Redshift — and more importantly, a natural stepping stone into AI.

![alt text](image.png)

Since my first degree was in Computer Science, most of the coursework felt like a refresher, but three areas genuinely grabbed my attention:

- **Machine Learning Techniques** — traditional ML, deep learning, feature engineering, and the mechanics of tuning a model.
- **Statistical Analysis** — regression, multivariate models, and the math that makes ML tick.
- **Business Major** — and this is where I caught the stock market bug.

## Enter AI

As it turned out, the MSc in Big Data gave me the perfect foundation to actually *get* what AI was doing under the hood. Then AI stopped being a buzzword and started being a real shift, and as an IT professional, the question became: *where do I fit while the ground is moving beneath me?* What I discovered was simple — AI, much like robots, still needs humans for end-to-end automation. So my job wasn't to worry about the future but to figure out how to adapt my experience and workflows using AI.

## AI at Work

The org I joined embraced AI tools early — Kiro, GitHub Copilot, GitHub Copilot Review — and one thing stayed constant throughout: **to build anything, you need context and knowledge**. A human in the loop is still required for security, verification, and all the stuff that keeps systems from falling apart.

What we realised over time was that without context, you're just burning tokens on problems a reusable workflow could solve. Think about it — IaC and automation have been DevOps pillars for years, and every single day you spend in an org is another day you're gaining context about your systems, your team processes, and your domain.

As we evolved as a team, each of us adopted these AI tools to boost productivity in our own way. This repo is where I document that journey — capturing the detail that helps someone out there understand the tradeoffs.

## My Lens

I look at AI through three angles: **cost**, **productivity**, and **practices**.

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

- **GitHub Copilot** — code review, implementation assistance
- **Kiro** — design/spec generation, incident analysis, MCP context integration
- **GitHub CLI** — PR creation, branch management
- **MCP Tools** — Jira, Confluence, and Terraform repo context
