---
title: Infrastructure Delivery
nav_order: 3
---

# Infrastructure Delivery Workflow (AI-Assisted SDLC)

End-to-end AI-assisted infrastructure delivery from Jira task through to merged pull request.

## Workflow Steps

1. **Jira task** — e.g. network/subnet deployment, aligned with Confluence architecture
2. **Feature branch** — Kiro creates a feature branch linked to the Jira ticket
3. **Design & spec generation** — AI-assisted using MCP context (Jira, Confluence, Terraform repo)
4. **Terraform implementation** — Design → Spec → Implementation
5. **PR creation** — Via GitHub CLI
6. **Copilot-assisted review** — Automated code review feedback
7. **Human approval and merge** — Final sign-off and merge to main

## Context Sources (MCP)

- **Jira** — Task requirements, acceptance criteria, linked tickets
- **Confluence** — Architecture decisions, network diagrams, naming conventions
- **Terraform repo** — Existing modules, state structure, variable patterns
