---
name: clawbridge
description: Trigger Clawbridge runner to find business connections
metadata:
  openclaw:
    emoji: "🌉"
    homepage: "https://clawbridge.cloud"
    requires:
      bins: ["clawbridge"]
    install:
      - id: "install-script"
        kind: "shell"
        label: "Install via script (recommended)"
        command: "curl -fsSL https://clawbridge.cloud/install | bash"
        bins: ["clawbridge"]
---

# Clawbridge Skill

> **Minimal trigger** for the Clawbridge runner from OpenClaw chat.

## What This Skill Does

This skill is a **thin adapter** that triggers the Clawbridge runner on your local machine.

**The skill does NOT do discovery.** All discovery logic lives in the runner (private).

The skill:
1. Runs `clawbridge run` via exec
2. Parses `VAULT_URL=` from stdout
3. Returns the link

## Usage

```
/clawbridge
```

Or with a profile:

```
/clawbridge --profile myprofile
```

## How It Works

```
User: /clawbridge
           │
           ▼
┌─────────────────────────────────────┐
│  Skill: exec clawbridge run         │
└─────────────────────────────────────┘
           │
           ▼
┌─────────────────────────────────────┐
│  Runner: Execute discovery          │
│  - Build prompts (private logic)    │
│  - Call OpenClaw as worker          │
│  - Upload to Vault                  │
│  - Print VAULT_URL=...              │
└─────────────────────────────────────┘
           │
           ▼
┌─────────────────────────────────────┐
│  Skill: Parse VAULT_URL, reply      │
└─────────────────────────────────────┘
```

## Output

```
✅ Found 3 candidates

View results: https://clawbridge.cloud/app/workspaces/xxx/runs/xxx
```

## Setup

Before using this skill:

```bash
# 1. Install runner
curl -fsSL https://clawbridge.cloud/install | bash

# 2. Link workspace
clawbridge link CB-XXXXXX

# 3. Configure profile in ~/.clawbridge/config.yml
```

## Architecture Notes

- **Skill = optional trigger** (only for OpenClaw chat)
- **Runner = product engine** (owns discovery strategy)
- **Runner invokes OpenClaw** as a worker to perform web search/fetch
- Discovery prompts are private (in runner, not skill)

## Schema

The Connection Brief schema is in `schema/connection_brief.json`.
