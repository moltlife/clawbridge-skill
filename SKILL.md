---
name: clawbridge
description: Find high-signal business connections and publish results to your Clawbridge Vault
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
      - id: "download-macos-arm64"
        kind: "download"
        label: "Download for macOS Apple Silicon"
        url: "https://github.com/moltlife/clawbridge-cli/releases/latest/download/clawbridge-macos-arm64"
        bins: ["clawbridge"]
      - id: "download-macos-x64"
        kind: "download"
        label: "Download for macOS Intel"
        url: "https://github.com/moltlife/clawbridge-cli/releases/latest/download/clawbridge-macos-x64"
        bins: ["clawbridge"]
      - id: "download-linux-x64"
        kind: "download"
        label: "Download for Linux x64"
        url: "https://github.com/moltlife/clawbridge-cli/releases/latest/download/clawbridge-linux-x64"
        bins: ["clawbridge"]
      - id: "download-linux-arm64"
        kind: "download"
        label: "Download for Linux ARM64"
        url: "https://github.com/moltlife/clawbridge-cli/releases/latest/download/clawbridge-linux-arm64"
        bins: ["clawbridge"]
---

# Clawbridge Skill

> **OpenClaw adapter for Clawbridge** - Run discovery and view results in your Vault

## What This Skill Does

This skill is a **thin adapter** that triggers the Clawbridge runner on your local machine. 

The skill does NOT do discovery itself. Instead it:
1. Executes `clawbridge run` on your machine
2. Parses the output to extract `VAULT_URL`
3. Returns the Vault link so you can view your candidates

All discovery logic lives in the **clawbridge runner** (installed locally).

## Usage

```
/clawbridge
```

Or with a specific profile:

```
/clawbridge --profile myprofile
```

## What Happens

1. **Skill checks** if `clawbridge` binary is installed
2. **Skill runs** `clawbridge run` (or `clawbridge run --profile <name>`)
3. **Runner executes** discovery via OpenClaw (web searches, page fetches, etc.)
4. **Runner uploads** results to your Vault
5. **Skill extracts** `VAULT_URL=...` from stdout
6. **Skill replies** with the Vault link and candidate count

## Output

The skill returns:

```
✅ Found 3 candidates

View results: https://clawbridge.cloud/app/workspaces/xxx/runs/xxx
```

## Setup Requirements

Before using this skill, you need to:

### 1. Install the runner

```bash
curl -fsSL https://clawbridge.cloud/install | bash
```

### 2. Link your workspace

Get a connect code from [clawbridge.cloud](https://clawbridge.cloud) and run:

```bash
clawbridge link CB-XXXXXX
```

### 3. Configure your profile

Edit `~/.clawbridge/config.yml` with your project profile:

```yaml
project_profile:
  offer: "What you offer"
  ask: "What you're looking for"
  ideal_persona: "Your target contacts"
  verticals: ["keyword1", "keyword2"]
```

## Commands

### List profiles

```
/clawbridge profiles
```

Runs `clawbridge profiles` to show available profiles.

### Run discovery

```
/clawbridge
/clawbridge --profile default
```

Runs `clawbridge run` and returns the Vault URL.

## Technical Details

### Skill Execution Flow

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
│  - Load profile from config                                 │
│  - Call OpenClaw for web search/fetch                       │
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

### Machine-Readable Output

The runner prints structured output that the skill parses:

```
PROFILE=default
JSON_PATH=/path/to/run.json
MD_PATH=/path/to/run.md
CANDIDATES_COUNT=3
VAULT_URL=https://clawbridge.cloud/app/workspaces/xxx/runs/xxx
```

The skill specifically looks for:
- `VAULT_URL=` to get the results link
- `CANDIDATES_COUNT=` to show how many matches were found

## Schema

The Connection Brief schema is in `schema/connection_brief.json`. This defines the structure of results uploaded to the Vault.

## License

MIT License
