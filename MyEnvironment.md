---
title: My AI Environment
nav_order: 2
---

# My AI-Enabled Engineering Workflow

## The Day Job

I'm a Senior Cloud Platform Engineer working within an organisation that manages 40+ AWS accounts across multiple product teams, each operating dev/test/prod environments. We're a team of three that owns and operates the AWS platform — our role is to enable product teams to run their workloads in a secure, governed, and self-service manner. We function as the **Cloud Center of Excellence**.

### Workstreams

- **Network Remediation & Segmentation** — Establishing a shared networking model for consistent adoption across product teams
- **Governance & Identity** — AWS Organisations, OU structure, SCPs, Control Tower, and migrating manual access workflows to Terraform-managed IAM Identity Center permission sets with Entra ID
- **Platform Security & Observability** — Security posture across the estate, plus an observability strategy for workloads with Jira-integrated alerting and incident tracking
- **IaC & Repository Strategy** — Breaking apart mono-repos into Terramate stacks to reduce sprawl and improve blast radius isolation
- **Migration & Modernisation** — Migrating legacy Linux workloads from managed datacentres into AWS with a clear roadmap for modernisation post-migration
- **Product Team Enablement** — Assisting product teams to refactor legacy applications toward cloud-native services where feasible
- **Tooling & Licensing** — GitHub team management, Copilot licensing governance, and Kiro access across AWS

### Daily Tooling

- **Jira** — Tickets and projects. We run Kanban with Epics, Tasks, and Sub-tasks to track platform work.
- **Confluence** — Documentation hub. Runbooks, architecture decisions, onboarding guides.
- **Terraform** — Infrastructure as Code. Every change goes through code.
- **GitHub** — CI/CD pipelines, pull requests, and GitHub Copilot Review for automated code feedback.
- **Rovo** — Atlassian's AI assistant for discovering documentation and tickets across the suite — useful when I need to find something outside of my current Kiro session.

## The AI Layer

I use two AI coding assistants daily:

- **Kiro CLI** — my primary agent, running inside WSL on Windows 11. Connects to Jira, Confluence, AWS, Terraform docs, and CloudWatch through MCP servers.
- **GitHub Copilot** — code completion, inline suggestions, and automated PR review via GitHub Copilot Review.

Both tools share context through **Kiro SPECS documents** — structured specification files that describe what needs to be built, the tasks involved, and the constraints. SPECS work as a bridge between Kiro and GitHub Copilot, so either tool can pick up where the other left off.

---

## Setting up my WSL environment

Everything below runs inside **WSL (Ubuntu) on Windows 11**. WSL gives me a native Linux shell where Kiro, the AWS CLI, Granted, Terraform, and Docker all behave the way they would on a Linux workstation — while I keep Windows for everything else.

### Installing Kiro

Kiro CLI is installed once inside WSL and then available from any directory:

```bash
# Install the Kiro CLI (one-time, inside WSL)
curl -fsSL <KIRO_INSTALL_URL> | bash

# Confirm it's on PATH
kiro --version

# Authenticate (opens a browser consent flow on first run)
kiro login
```

After install, I start a session simply by running `kiro` from whichever folder holds the work I'm doing. There's no per-project install step — the binary is global, and so is its configuration.

### One global `~/.kiro` folder for everything

The key to a consistent experience across every project is that Kiro reads its configuration from a **single global folder in my home directory**, not from each repo. Whatever directory I launch a session from, the same skills, MCP servers, and steering are loaded.

```
~/.kiro/
├── settings/
│   └── mcp.json          # MCP servers (Jira/Confluence, AWS, Terraform, CloudWatch)
├── skills/               # reusable, on-demand capabilities
│   ├── aws-multi-account/
│   │   └── SKILL.md
│   └── jira-formatting/
│       └── SKILL.md
└── steering/             # rules loaded on every turn (kept lean)
    └── *.md
```

Because this lives in `~/.kiro/` and not in a repo, I get the same tools whether I'm in an infrastructure repo, a scratch folder, or a ticket folder. Install once, use everywhere. (The MCP side of this is covered in [MCP Server Configuration](#mcp-server-configuration) below — the same "global, not per-project" principle applies to both skills and MCP servers.)

### A `jira-issues` folder, one subfolder per ticket

I keep a dedicated working area for ticket-driven work. Each piece of work gets its own folder, **prefixed with the Jira ticket number followed by a short description**:

```
~/jira-issues/
├── SD-57556-enable-vpc-flow-logs/
├── PLA-396-kiro-usage-reporting/
└── SD-62022-acm-acme-cert-renewal/
```

Launching a Kiro session from inside one of these folders gives me the clean "one session per problem space" model described in [Session Management](#session-management): the folder name alone tells both me and the agent which ticket we're on, and any SPECs or notes for that ticket live right beside it. The ticket-number prefix keeps the folders sorted and trivially greppable, and it makes the link back to Jira unambiguous.

### My troubleshooting approach: context first, fix second

The habit that makes the biggest difference isn't any single tool — it's **gathering context before touching anything**. A ticket is a starting point, not the full picture. My default loop:

1. **Read the ticket.** Kiro pulls the full Jira issue through the Atlassian MCP — description, acceptance criteria, linked issues, and comments.
2. **If the ticket is vague, go find context.** Kiro reads the relevant **Confluence documentation via the Atlassian MCP** to understand the history, the architecture decisions, and any prior incidents around the thing I'm about to change. This is where a one-line ticket turns into a real case: *what is this system, why does it exist, what's changed before.*
3. **Check whether the change is actually in code.** Because we manage our infrastructure with **Terraform/Terramate**, Kiro reads the relevant repo to confirm whether the resource in question is defined in code or was created out-of-band. This single check avoids the classic trap of "fixing" something in the console that Terraform will revert on the next apply — or worse, drifting state.
4. **Build the case, then break it into tasks.** With the ticket, the documentation, and the code all in context, Kiro produces a SPEC: problem statement, approach, and the tasks to get there.

> The point is that **context matters more than just solving the ticket**. Looking the information up in the places where more context lives — Confluence for the "why", the Terraform repo for the "what's really deployed" — produces a fix that's correct *and* durable, rather than one that closes the ticket but causes drift or misses the actual root cause.

### The skills and tools I lean on

These live in `~/.kiro/skills/` and load **on demand** — their metadata is always visible to the agent, but the full body only loads when a task matches. (The token tradeoff behind that behaviour is explained in [Steering vs Skills](#steering-vs-skills-in-my-setup) below, and in detail on the [Agent Context — Steering vs Skills](AgentContext.md) page.)

- **`aws-multi-account`** — runs AWS CLI commands across *all* accounts via the Granted-based helper. The AWS MCP is one profile per session, so when a task fans out across the whole estate, this skill is the way to do it. See [Working Across Many Accounts with Granted](#working-across-many-accounts-with-granted) for the helper itself.
- **`jira-formatting`** — when writing to Jira (comments, descriptions, status updates), this skill makes the agent use Jira's rich display features — panels, status lozenges, tables, headings, code marks — so updates are clear and meaningful instead of a wall of plain text.

Both are reusable building blocks: they encode "how we do a recurring thing here" once, so I don't re-explain it every session. Nothing account-specific is baked into them — profiles come from Granted, and endpoints/paths are read from environment variables or placeholders.

### Steering vs Skills in my setup

A session only acts on what's in its **context**. A helper sourced in `~/.bashrc` is available to the *shell*, but the agent won't know it exists unless that knowledge is somewhere Kiro loads automatically. There are two mechanisms, and the choice is about **token cost vs. availability**:

| | Steering file | Skill |
|---|---|---|
| Location | `~/.kiro/steering/*.md` | `~/.kiro/skills/<name>/SKILL.md` |
| Loaded | Full content, **every turn** | Metadata always; body **on demand** |
| Idle token cost | Whole file, every turn | Just the name + description line |
| Best for | Rules that shape *every* reply | Occasional reference / how-to |

**Rule of thumb:** if it must influence *every* response (coding standards, PR conventions), use a lean steering file. If it's "knowledge to reach for when a specific task comes up" (like the multi-account helper), use a skill. This is exactly why `aws-multi-account` and `jira-formatting` are skills, not steering — I don't pay for them on turns where they're irrelevant.

> For the full explanation — including how the skill *description* acts as the trigger, and a worked example — see [Agent Context — Steering vs Skills](AgentContext.md).

---

## The Workflow: From Ticket to Merged PR

Here's how a typical infrastructure ticket flows through my AI-enabled workflow:

### Example: "Enable VPC Flow Logs for all production accounts"

```
┌──────────┐     ┌──────────┐     ┌──────────┐     ┌──────────┐     ┌──────────┐
│  1. Jira │────▶│  2. SPEC │────▶│3. Implement────▶│ 4. PR    │────▶│ 5. Merge │
│  Ticket  │     │  & Plan  │     │  Changes │     │  Review  │     │  & CI/CD │
└──────────┘     └──────────┘     └──────────┘     └──────────┘     └──────────┘
```

**Step 1 — Read the ticket**

Kiro reads the Jira ticket directly through the Atlassian MCP. I don't copy-paste anything — it pulls the full context including acceptance criteria, linked issues, and comments.

```
> "Read SD-57556 and summarise what's needed"
```

Kiro fetches the ticket, understands the scope, and asks clarifying questions if the requirements are ambiguous.

**Step 2 — Generate a SPEC**

I ask Kiro to produce a SPEC document (or a `/plan` in GitHub Copilot terms). The SPEC contains:
- Problem statement (from the ticket)
- Proposed approach
- Tasks broken into implementation steps
- Constraints and considerations

At this stage, Kiro analyses the local codebase — I give it the repo path and it maps the existing Terraform module structure, identifies where changes belong, and understands naming conventions. **It doesn't make changes yet.** This is pure analysis and planning.

```
> "Generate a spec for this ticket. The repo is at ~/repos/terraform-aws-platform"
```

The SPEC becomes a shared artefact that both Kiro and GitHub Copilot can reference.

**Step 3 — Implement**

Once I'm happy with the SPEC, I tell Kiro to implement. It:
- Creates a feature branch
- Writes the Terraform code following existing patterns in the repo
- Uses the AWS MCP to validate resource configurations against live state
- References Terraform provider docs through the Terraform MCP for correct syntax

All of this runs under **my credentials** — Kiro is acting as me. The git commits, the AWS API calls, the branch creation — it's all my identity.

```
> "Implement the spec. Create a feature branch and push when done."
```

**Step 4 — PR Review**

Kiro pushes the feature branch and creates a draft PR. GitHub Copilot Review automatically runs on the PR — checking for:
- Security issues
- Code style and best practices
- Potential bugs or misconfigurations

This gives me a first-pass automated review before a human even looks at it.

**Step 5 — Human approval and merge**

A team member reviews the PR (with Copilot's annotations as context), approves, and merges. The code flows into our existing CI/CD pipeline — `terraform plan` on PR, `terraform apply` on merge to main.

---

## Why SPECS Matter

SPECS are the glue between tools:

- **Kiro** generates them from Jira context + codebase analysis
- **GitHub Copilot** uses them as implementation guides during code completion
- **Humans** review them before implementation starts — catching design issues early
- **Future you** reads them to understand why something was built a certain way

A SPEC isn't overhead — it's the checkpoint that stops AI from building the wrong thing fast.

---

## Session Management

Each Jira ticket or project gets its own folder and Kiro session. This keeps context clean — one session per problem space, no bleed between unrelated work.

When a task grows beyond what a single session can handle, I break it out into a separate Kiro window. I solve the sub-problem in that dedicated session, capture the output as a SPEC document, and feed it back into the original ticket session as structured context. This way, complex work gets decomposed without losing coherence in the main thread.

In practice:

```
┌─────────────────────┐       ┌─────────────────────┐
│  Main Session       │       │  Breakout Session   │
│  (Jira ticket)      │       │  (Sub-problem)      │
│                     │       │                     │
│  "This piece is     │──────▶│  Solve it here      │
│   too big..."       │       │  Output → SPEC doc  │
│                     │◀──────│                     │
│  Feed SPEC back in  │       └─────────────────────┘
└─────────────────────┘
```

This pattern keeps each session focused, preserves token efficiency, and ensures that the AI always has the right context for the task at hand.

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
        "AWS_REGION": "<TARGET_REGION>",
        "FASTMCP_LOG_LEVEL": "ERROR"
      }
    }
  }
}
```

Config lives at `~/.kiro/settings/mcp.json`.

This is a **global configuration** — not per-project or per-session. Every Kiro session loads the same MCP servers automatically, regardless of which directory you're working in. This ensures that Jira, Confluence, AWS, Terraform, and CloudWatch are always available without manual setup each time.

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
| **Auth** | IAM SigV4 via a local proxy (`mcp-proxy-for-aws`) that bridges **your** IAM credentials to MCP's OAuth 2.1. Every call runs as you, with your permissions — no shared/service identity |
| **Limitation** | Only reaches profiles declared at startup. The proxy supports multiple profiles (`--profile a b c`) with per-call switching via the `aws_profile` parameter, but you must list them up front — it can't reach an undeclared account. Declaring all 50+ accounts at once failed to start (root cause not investigated), so keep the set small and relevant |
| **Tip** | For a few related accounts, declare them all and switch per call. For true all-estate fan-out, drop to the Granted `for_each` helper instead. The MCP service endpoint is always `us-east-1` (or `eu-central-1`) regardless of target region — the `--region` flag controls where your API calls go, not where the MCP service lives |

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

- **Switch to *any* account dynamically mid-session** — the AWS MCP proxy *does* support per-call profile switching via the `aws_profile` tool parameter, but only across the profiles you **declare at startup** (`--profile a b c ...`). It can't reach an account you didn't list when the session began. In practice I keep the config to a small, relevant set: when I tried to pre-declare all 50+ accounts at once, the proxy failed to start. I haven't dug into the root cause — the practical takeaway is that a large declared set isn't viable, so for all-estate work I use the Granted helper below.
- **Cross-account queries across the *whole* estate in one shot** — the multi-profile proxy covers the handful of accounts you declared, not all 50+. Fanning out across everything is a job for the Granted `for_each` helper, not the MCP.
- **Persistent Atlassian sessions** — OAuth tokens can go stale; occasional `/mcp logout` + `/mcp auth` needed. (The config still uses the `/v1/mcp` endpoint, which is on a deprecation path — expect this to need revisiting.)
- **Run Terraform plan/apply** — the Terraform MCP is docs-only, not an execution engine

> **Whose credentials does the AWS MCP use?** Mine. The proxy authenticates with the AWS profiles I pass it (`--profile ...` / `AWS_PROFILE`), which are my Granted/SSO profiles resolving to my IAM Identity Center identity. Every call is signed with **SigV4 using my local credentials**, so the MCP acts **as me** with exactly my permissions — never a shared or service identity. This is the same "Kiro is acting as me" principle that applies to the git commits and API calls in the workflow above.

Despite these limitations, this setup has genuinely changed how I work. The ability to stay in one terminal — read a Jira ticket, generate a spec, implement against a local repo, push a branch, and get an automated review — without context-switching between browser tabs is the real productivity win.

---

## Working Across Many Accounts with Granted

The MCP limitations above (only the profiles you declare at startup are reachable, and declaring the whole estate isn't practical) are a real constraint when you operate a large AWS estate. Outside of MCP, though, the terminal itself can still work across every account at once. I use [Granted](https://granted.dev/) to manage SSO profiles for all my AWS accounts, and I've centralised a small helper so any shell — or Kiro session — can log in and run commands across the whole estate.

The idea: keep the profile list and the login logic in **one** place, source it everywhere, and never hand-maintain a list of accounts again.

### Why keep the helper if the MCP does multiple profiles?

A reasonable question, now that the AWS MCP proxy supports multiple declared profiles. They solve different problems:

| | AWS MCP (multi-profile) | Granted `for_each` helper |
|---|---|---|
| Best at | Interactive, agent-driven work on a **few** related accounts | Running one command across **all** accounts in a single pass |
| Account set | Declared at startup; can't reach an undeclared account | Generated from the Granted config, so it tracks whatever profiles exist |
| Large estate | Declaring all 50+ at once failed to start for me | Designed for estate-wide fan-out |
| Runs in | Kiro session only | Any shell, script, or CI job |
| Pattern | Switch the active profile per call | Same command against every account, results collected |

In short: the MCP is for *"work in these specific accounts with the agent"*; the helper is for *"answer a question across the whole estate."* The proxy's multi-profile support reduces how often I need the helper for small multi-account tasks, but it doesn't replace it for estate-wide work.

### Layout

```
~/.local/share/myorg/
├── myorg-profiles.sh   # helper library (functions + generator)
└── profiles.txt        # generated list of accounts (never hand-edited)
```

The helper auto-loads in every new shell/Kiro session via a small block added to `~/.bashrc`:

```bash
# --- AWS infra helpers ---
if [ -f "$HOME/.local/share/myorg/myorg-profiles.sh" ]; then
    . "$HOME/.local/share/myorg/myorg-profiles.sh"
fi
```

This is additive — it only defines functions and runs nothing on startup. To load it into your current shell without opening a new one: `source ~/.bashrc`.

### How to use it in any session

```bash
# Log into all accounts (starts the Granted SSO session)
myorg_login

# Print the profile list
myorg_profiles

# Run any AWS command across every account
myorg_for_each aws sts get-caller-identity --query Account --output text

# Regenerate the list after accounts are added/removed in Granted
myorg_generate_profiles
```

Or from a standalone script:

```bash
source ~/.local/share/myorg/myorg-profiles.sh
myorg_login
for p in $(myorg_profiles); do
    AWS_PROFILE="$p" aws ec2 describe-vpcs --query 'Vpcs[].VpcId' --output text
done
```

### Why this pattern works

- **Self-updating** — `myorg_profiles` generates the list from your Granted config on first use, so it never goes stale as accounts are added or removed.
- **Login built in** — `myorg_login` wraps the Granted SSO command so you don't have to remember the flags:

  ```bash
  granted sso login --sso-region <SSO_REGION> --sso-start-url <SSO_START_URL>
  ```

- **Overridable** — environment variables (e.g. `MYORG_GRANTED_CONFIG`, `MYORG_EXCLUDE`, `MYORG_SSO_START_URL`) let you point it at a different Granted registry or SSO endpoint without editing the script.

### The generator (reference)

The core of the helper is a generator that reads the Granted config and emits a clean profile list, plus a few convenience functions. Values like the SSO region and start URL are read from environment variables so nothing environment-specific is baked into the script:

```bash
# Configuration (override via environment)
MYORG_DIR="${MYORG_DIR:-$HOME/.local/share/myorg}"
MYORG_GRANTED_CONFIG="${MYORG_GRANTED_CONFIG:-$HOME/granted/<registry>/config}"
MYORG_PROFILES_FILE="${MYORG_PROFILES_FILE:-$MYORG_DIR/profiles.txt}"
MYORG_SSO_REGION="${MYORG_SSO_REGION:-<SSO_REGION>}"
MYORG_SSO_START_URL="${MYORG_SSO_START_URL:-<SSO_START_URL>}"

# Regenerate profiles.txt from the Granted config
myorg_generate_profiles() {
    grep '^\[profile ' "$MYORG_GRANTED_CONFIG" \
        | sed -E 's/\[profile (.*)\]/\1/' \
        | sort > "$MYORG_PROFILES_FILE"
}

# Print the profile list (skipping comments/blanks)
myorg_profiles() {
    grep -vE '^\s*(#|$)' "$MYORG_PROFILES_FILE"
}

# Ensure an active Granted SSO session
myorg_login() {
    granted sso login \
        --sso-region "$MYORG_SSO_REGION" \
        --sso-start-url "$MYORG_SSO_START_URL"
}

# Run a command for every account profile
myorg_for_each() {
    local profile
    while IFS= read -r profile; do
        echo "=== $profile ===" >&2
        AWS_PROFILE="$profile" "$@"
    done < <(myorg_profiles)
}
```

This turns the "one profile per session" MCP limitation into a non-issue for scripted, cross-account work: MCP stays single-account for interactive AI tasks, while the CLI helper handles fan-out across the whole estate.
