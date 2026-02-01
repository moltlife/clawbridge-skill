# claw-clawbridge

> OpenClaw skill for discovering warm introduction opportunities

Nightly connection briefs for agencies: discover 3–10 warm intros worth pursuing, with evidence, message drafts, and a human approval workflow.

## What It Does

Given a project profile defining what you offer and who you're looking for, this skill will:

1. **Scout** the web for potential connection opportunities
2. **Filter** candidates based on hard requirements
3. **Rank** by relevance, intent, credibility, and engagement potential
4. **Draft** personalized outreach messages
5. **Output** structured Connection Briefs for human review

## Installation

### With OpenClaw

```bash
# Add this skill to your OpenClaw configuration
openclaw skill add claw-clawbridge

# Or reference directly
openclaw skill add https://github.com/YOUR_USERNAME/clawbridge-skill
```

### Manual Installation

1. Clone this repository
2. Point your OpenClaw config to the `SKILL.md` file
3. Configure your project profile (see below)

## Quick Start

1. **Create a Project Profile**

```yaml
offer: "We help B2B SaaS companies automate their content operations"
ask: "Marketing partners, agency relationships, content-focused companies"
ideal_persona: "VP Marketing or Head of Content at Series A-C startups"
verticals:
  - "B2B SaaS"
  - "content marketing"
  - "marketing automation"
tone: "friendly, professional"
```

2. **Run the Skill**

```bash
# Via OpenClaw
openclaw run claw-clawbridge --profile ./profile.yaml

# Output will be saved to:
# - ./output/run.json (structured data)
# - ./output/run.md (human-readable report)
```

3. **Review the Connection Brief**

Open `run.md` to see your top candidates with:
- Why they match
- Evidence links
- Draft messages
- Recommended actions

## Project Structure

```
clawbridge-skill/
├── SKILL.md                 # OpenClaw skill entry point
├── prompts/
│   ├── discovery.md         # Web search strategy
│   ├── filtering.md         # Candidate filtering rules
│   ├── ranking.md           # Scoring heuristics
│   └── drafting.md          # Message drafting guidelines
├── schema/
│   └── connection_brief.json # JSON schema for output
├── examples/
│   ├── sample_run.json      # Example JSON output
│   └── sample_run.md        # Example human-readable report
├── venues/
│   ├── moltbook.md          # Moltbook search strategy
│   ├── web.md               # General web search
│   └── communities.md       # Community/forum discovery
└── README.md                # This file
```

## Configuration

### Project Profile Fields

| Field | Required | Description |
|-------|----------|-------------|
| `offer` | Yes | What your company/agency offers |
| `ask` | Yes | What you're looking for (partners, clients, etc.) |
| `ideal_persona` | Yes | Description of target personas |
| `verticals` | Yes | 3-8 industry keywords |
| `geo_timezone` | No | Geographic preferences |
| `disallowed` | No | Do-not-contact list |
| `tone` | No | Style guidance for draft messages |

### Run Budget

Control resource usage per run:

```yaml
run_budget:
  max_searches: 20      # Maximum web searches
  max_fetches: 50       # Maximum page fetches
  max_minutes: 10       # Maximum run duration
```

### Environment Variables

```bash
CLAWBRIDGE_TOP_K=3              # Candidates to return (default: 3)
CLAWBRIDGE_RECENCY_DAYS=30      # Activity threshold (default: 30)
```

## Output

### JSON Schema

See [`schema/connection_brief.json`](./schema/connection_brief.json) for the full schema.

Key fields:
- `workspace_id` - Your workspace identifier
- `run_id` - Timestamp of the run
- `candidates` - Array of top-K candidates with evidence and drafts
- `next_actions` - Recommended actions per candidate

### Markdown Report

Human-readable format with:
- Summary and key insights
- Candidate profiles with scores
- Evidence links
- Draft messages
- Decision log template

## Security

This skill follows OpenClaw security best practices:

- ✅ No secrets in prompts (use env/config)
- ✅ Strict tool allowlists (only `web_search`, `web_fetch`, `browser`)
- ✅ Human-in-the-loop (never auto-send outreach)
- ✅ Rate limiting (respects run budgets)
- ✅ Avoid list enforcement

## Candidate Selection

### Hard Requirements

Candidates are discarded if missing:
- At least 2 evidence URLs
- Clear mapping to your `ask`
- Activity within recency threshold

### Ranking Factors

| Factor | Weight | Description |
|--------|--------|-------------|
| Relevance | 30% | Match to keywords and ask |
| Intent | 25% | Active signals (hiring, seeking, building) |
| Credibility | 20% | Consistent presence across sources |
| Recency | 15% | Recent activity |
| Engagement | 10% | Shared interests/communities |

## Examples

See the [`examples/`](./examples/) directory for sample outputs.

## Contributing

Contributions welcome! Please ensure changes:

1. Maintain deterministic output schema
2. Keep secrets out of prompts
3. Preserve human-in-the-loop requirement
4. Include evidence-based selection

## License

MIT License - see LICENSE for details.

## Related Projects

- **clawbridge-runner** - CLI to run this skill on a schedule
- **clawbridge-web** - Web UI for workspace management and vault review
