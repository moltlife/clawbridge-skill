# claw-clawbridge

> OpenClaw adapter skill for Clawbridge - triggers the local runner and returns Vault results

## What This Skill Does

This skill is a **thin adapter** for OpenClaw that triggers the Clawbridge runner on your local machine.

**The skill does NOT do discovery itself.** It simply:
1. Executes `clawbridge run` via the OpenClaw `exec` tool
2. Parses the output to extract `VAULT_URL` and `CANDIDATES_COUNT`
3. Returns the Vault link so you can view your candidates

All discovery logic lives in the **clawbridge runner** (installed locally).

## Quick Start

### 1. Install the Runner

```bash
curl -fsSL https://clawbridge.cloud/install | bash
```

### 2. Link Your Workspace

Get a connect code from [clawbridge.cloud](https://clawbridge.cloud):

```bash
clawbridge link CB-XXXXXX
```

### 3. Configure Your Profile

Edit `~/.clawbridge/config.yml`:

```yaml
project_profile:
  offer: "What you offer"
  ask: "What you're looking for"
  ideal_persona: "Your target contacts"
  verticals: ["keyword1", "keyword2"]
```

### 4. Use in OpenClaw

```
/clawbridge
```

Or with a specific profile:

```
/clawbridge --profile myprofile
```

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│  User: /clawbridge                                          │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  Skill: exec clawbridge run                                 │
│  (uses OpenClaw exec tool)                                  │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  Runner: Execute discovery workflow                         │
│  - Load profile from ~/.clawbridge/config.yml               │
│  - Call OpenClaw for web search/fetch (if available)        │
│  - Validate + rank candidates                               │
│  - Upload to Vault                                          │
│  - Print VAULT_URL=...                                      │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  Skill: Parse stdout for VAULT_URL=                         │
│  Reply with Vault link + summary                            │
└─────────────────────────────────────────────────────────────┘
```

## Output

When you run `/clawbridge`, the skill returns:

```
✅ Found 3 candidates

View results: https://clawbridge.cloud/app/workspaces/xxx/runs/xxx
```

## Runner Output Format

The runner prints structured output that the skill parses:

```
PROFILE=default
JSON_PATH=/path/to/run.json
MD_PATH=/path/to/run.md
CANDIDATES_COUNT=3
VAULT_URL=https://clawbridge.cloud/app/workspaces/xxx/runs/xxx
```

## Project Structure

```
clawbridge-skill/
├── SKILL.md                 # OpenClaw skill definition (adapter)
├── schema/
│   └── connection_brief.json # JSON schema for Vault uploads
├── examples/
│   ├── sample_run.json      # Example JSON output
│   └── sample_run.md        # Example human-readable report
├── prompts/                 # Reference prompts (used by runner)
├── venues/                  # Reference venue strategies (used by runner)
└── README.md                # This file
```

## Installation

### Via OpenClaw (Recommended)

```bash
openclaw skills install claw-clawbridge
```

### Manual

```bash
git clone https://github.com/moltlife/clawbridge-skill.git
cp -r clawbridge-skill ~/.openclaw/workspace/skills/claw-clawbridge
openclaw gateway restart
```

## Requirements

- **clawbridge runner** installed and linked (`curl -fsSL https://clawbridge.cloud/install | bash`)
- **OpenClaw** installed (`npm install -g openclaw@latest`)

## Related Projects

- [**clawbridge-runner**](https://github.com/moltlife/clawbridge-runner) - The CLI that does the actual discovery
- [**clawbridge-web**](https://github.com/moltlife/clawbridge-web) - Web UI for workspace management and Vault
- [**clawbridge-cli**](https://github.com/moltlife/clawbridge-cli) - Public releases of the runner binary

## License

MIT License
