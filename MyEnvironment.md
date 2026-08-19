---
title: My Environment
nav_order: 2
---

# My AI-Enabled Engineering Workflow

## The Day Job

I'm a Cloud Platform Engineer working inside an organisation that supports 40+ AWS accounts across product teams running dev/test/prod environments. Our platform team operates as the **Cloud Center of Excellence** — we set the guardrails, build the shared infrastructure, and enable product teams to ship safely.

A typical day looks like this:

- **Jira** — Tickets and projects. We run Kanban with Epics, Tasks, and Sub-tasks to track platform work.
- **Confluence** — Documentation hub. Runbooks, architecture decisions, onboarding guides.
- **Terraform** — Infrastructure as Code. Every change goes through code.
- **GitHub** — CI/CD pipelines, pull requests, and GitHub Copilot Review for automated code feedback.
- **Rovo** — Atlassian's AI assistant for discovering documentation and tickets across the suite — useful when I need to find something outside of my current Kiro session.

## The AI Layer

I run **Kiro CLI** as my primary AI coding assistant inside WSL on Windows 11. Kiro connects to multiple backend services through MCP (Model Context Protocol) servers — giving it live access to Jira, Confluence, AWS, Terraform docs, and CloudWatch.

This isn't a demo setup. It's what I use daily to:
- Investigate incidents using CloudWatch logs and metrics
- Search and read Jira tickets without leaving the terminal
- Look up Terraform provider docs while writing modules
- Query AWS APIs across accounts for troubleshooting
- Draft and update Confluence documentation

---

## MCP Server Configuration

MCP servers extend Kiro's capabilities by connecting it to external tools and services. Here's my setup:

```json
{
  "mcpServers": {
    "atlassian": {
      "type": "http",
      "url": "https://mcp.atlassian.com/v1/mcp"
    },
    "aws-mcp": {
      "command": "uvx",
      "args": [
        "mcp-proxy-for-aws@1.6.3",
        "https://aws-mcp.us-east-1.api.aws/mcp",
        "--profile", "<AWS_PROFILE_NAME>",
        "--region", "<TARGET_REGION>"
      ],
      "env": {
        "AWS_PROFILE": "<AWS_PROFILE_NAME>",
        "AWS_REGION": "<TARGET_REGION>"
      }
    },
    "terraform": {
      "command": "docker",
      "args": [
        "run", "-i", "--rm",
        "hashicorp/terraform-mcp-server:1.1.0"
      ]
    },
    "cloudwatch": {
      "command": "uvx",
      "args": [
        "awslabs.cloudwatch-mcp-server@latest"
      ],
      "env": {
        "AWS_PROFILE": "<AWS_PROFILE_NAME>",
        "AWS_REGION": "ap-southeast-2",
        "FASTMCP_LOG_LEVEL": "ERROR"
      }
    }
  }
}
```

Config lives at `~/.kiro/settings/mcp.json`.

---

## MCP Server Breakdown

### Atlassian (Jira + Confluence)

| | |
|---|---|
| **What it does** | Read/write Jira issues, search with JQL, read/edit Confluence pages, Rovo search across both |
| **Auth** | OAuth 2.1 — browser-based consent flow on first connect |
| **Limitation** | Uses the `/v1/mcp` endpoint (not `/authv2`) due to a known scope mismatch bug with the newer auth flow. Identity-level calls work on both, but resource-level tools only work on `/v1/mcp` |
| **Tip** | If you get `401 "scope does not match"` errors, run `/mcp logout atlassian` then `/mcp auth atlassian` to force a clean re-auth |

### AWS MCP Server

| | |
|---|---|
| **What it does** | Execute any of the 15,000+ AWS API operations, run sandboxed Python/boto3 scripts, search and read AWS documentation |
| **Auth** | IAM SigV4 via a local proxy (`mcp-proxy-for-aws`) that bridges your IAM credentials to MCP's OAuth 2.1 |
| **Limitation** | Locked to one AWS profile per session. With 40+ accounts managed via Granted, you need to assume the right profile before starting Kiro. No dynamic profile switching mid-session |
| **Tip** | The MCP service endpoint is always `us-east-1` (or `eu-central-1`) regardless of which region you're targeting. The `--region` flag controls where your API calls go, not where the MCP service lives |

### Terraform

| | |
|---|---|
| **What it does** | Search and read Terraform provider docs, module docs, and policy docs from the public registry |
| **Auth** | None — runs locally via Docker, no credentials required |
| **Limitation** | Documentation only. Cannot plan or apply Terraform. Useful for looking up resource arguments, data sources, and module inputs without leaving the terminal |
| **Tip** | Uses Docker — ensure the Docker daemon is running before starting Kiro |

### CloudWatch

| | |
|---|---|
| **What it does** | Query CloudWatch Logs Insights, get metric data, check active alarms, analyse log groups for anomalies, and get alarm recommendations |
| **Auth** | IAM credentials via `AWS_PROFILE` env var |
| **Limitation** | Same as aws-mcp — single profile per session. Also bound to one region at startup. For cross-region observability, you'd need to update the config and restart |
| **Tip** | Pairs well with aws-mcp. Use CloudWatch MCP for log/metric investigation, then aws-mcp for deeper API-level troubleshooting |

---

## How MCP Works (Simply)

```
┌─────────────┐         ┌───────────────┐         ┌─────────────────┐
│   Kiro CLI  │ ──MCP──▶│  MCP Server   │ ──API──▶│  External Tool  │
│  (AI Agent) │◀── ─────│  (Local/HTTP) │◀── ─────│  (AWS/Jira/etc) │
└─────────────┘         └───────────────┘         └─────────────────┘
```

- **Kiro** is the AI agent — it decides when to call tools based on your prompts
- **MCP Servers** are bridges — they translate between the AI and external services
- **External Tools** are the actual systems — AWS APIs, Jira REST API, Terraform Registry

Each MCP server exposes a set of **tools** (functions the AI can call). When you ask Kiro something like "show me the last 5 errors in my Lambda logs", it invokes the CloudWatch MCP's `execute_log_insights_query` tool behind the scenes.

---

## What This Setup Can't Do (Yet)

- **Switch AWS profiles mid-session** — you're locked to one account per Kiro session
- **Cross-account queries in one shot** — need to restart with a different profile
- **Persistent Atlassian sessions** — OAuth tokens can go stale; occasional `/mcp logout` + `/mcp auth` needed
- **Run Terraform plan/apply** — the Terraform MCP is docs-only, not an execution engine

Despite these limitations, this setup has genuinely changed how I work. The ability to stay in one terminal and pull context from Jira, AWS, Confluence, and Terraform docs — without context-switching between browser tabs — is the real productivity win.
