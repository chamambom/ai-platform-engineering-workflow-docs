---
title: Home
nav_order: 1
---

# My AI Adoption Journey — Strategy & Tips

## The Backstory

10 years as an Infrastructure & Cloud engineer (2010–2020). Python, MongoDB, Linux, VMware, Cisco, Dell, AWS, Azure — the whole stack. Certifications collected: RHCE, Azure & AWS Solutions Architect. But I'd gotten comfortable. I wanted to reboot my curiosity and re-learn *how to learn*.

## The MSc Bet

Around Covid, I enrolled for an MSc. I already had Cloud experience, so I needed something that *complemented* it rather than overlapped. AI, Cybersecurity, or Cloud Computing degrees get outdated fast — industry moves too quick. So I picked **Data Engineering**. Mature field, solid tooling (Databricks, Azure Data Factory, AWS Redshift), and a natural on-ramp to AI.

![alt text](image.png)

Having done my first degree in Computer Science, most courses were a refresher. Three areas grabbed me:

- **Machine Learning Techniques** — traditional ML, deep learning, feature engineering, model tuning.
- **Statistical Analysis** — regression, multivariate models, the math that underpins ML.
- **Business Major** — this is where I caught the stock market bug.

## Enter AI

The MSc in Big Data turned out to be the perfect foundation to *get* what AI was doing under the hood.

AI stopped being a buzzword and started being a shift. As an IT professional, the question became: *where do I fit while the ground is moving?* The answer was simple — AI (like robots) still needs humans for end-to-end automation. My job wasn't to fear the future but to adapt my experience and workflows using AI.

## AI at Work

The org I joined embraced AI tools early — Kiro, GitHub Copilot, GitHub Copilot Review. One thing stayed constant: **to build anything, you need context and knowledge**. A human in the loop is still required for security, verification, and keeping systems from falling apart.

Without context, you're just burning tokens on problems a reusable workflow could solve. IaC & automation have been DevOps pillars for years. Every day in an org = more context about your systems, processes, and domain.

As we evolved as a team, each of us adopted AI tools to boost productivity in different ways. This repo documents my journey — capturing the detail that helps someone out there understand the tradeoffs.

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
