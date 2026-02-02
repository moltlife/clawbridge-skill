# Clawbridge Skill

> **Minimal OpenClaw skill** to trigger the Clawbridge runner from chat.

## What This Is

This skill is a **thin adapter** that lets you trigger Clawbridge from OpenClaw chat:

```
/clawbridge
```

**The skill does NOT do discovery.** All discovery logic lives in the [clawbridge-runner](https://github.com/moltlife/clawbridge-runner) (private).

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│  User: /clawbridge                                          │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  Skill: exec clawbridge run                                 │
│  (this repo - just a trigger)                               │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  Runner: Execute discovery workflow                         │
│  - Build prompts (private logic)                            │
│  - Call OpenClaw/Clawdbot/Moltbot as worker                 │
│  - Validate candidates                                      │
│  - Upload to Vault                                          │
│  - Print VAULT_URL=...                                      │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  Skill: Parse VAULT_URL, reply with link                    │
└─────────────────────────────────────────────────────────────┘
```

**Key points:**
- **Skill = optional trigger** (only for OpenClaw chat integration)
- **Runner = product engine** (owns discovery strategy, prompts, ranking)
- **Runner invokes OpenClaw** as a worker to perform web search/fetch

## Setup

Before using this skill, you need the Clawbridge runner installed:

```bash
# 1. Install runner
curl -fsSL https://clawbridge.cloud/install | bash

# 2. Link your workspace (get code from clawbridge.cloud)
clawbridge link CB-XXXXXX

# 3. Configure your profile in ~/.clawbridge/config.yml
```

## Usage

In OpenClaw chat:

```
/clawbridge
```

Or with a specific profile:

```
/clawbridge --profile myprofile
```

## Output

The skill returns:

```
✅ Found 3 candidates

View results: https://clawbridge.cloud/app/workspaces/xxx/runs/xxx
```

## Files

```
clawbridge-skill/
├── SKILL.md           # Skill definition for OpenClaw
├── README.md          # This file
├── schema/
│   └── connection_brief.json    # Output schema
└── examples/
    ├── sample_run.json
    └── sample_run.md
```

## Schema

The Connection Brief schema (`schema/connection_brief.json`) defines the structure of discovery results uploaded to the Vault.

## Direct CLI Usage

You don't need this skill to use Clawbridge. You can run the runner directly:

```bash
clawbridge run
```

The skill only exists if you want to trigger runs from OpenClaw chat.

## License

MIT
