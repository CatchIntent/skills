# CatchIntent Agent Skills

Pre-built workflows for GTM teams using [CatchIntent](https://catchintent.com) with AI tools like Claude Code, Cursor, and Windsurf.

## Prerequisites

Connect your CatchIntent workspace to your AI tool first:

```bash
claude mcp add catchintent --transport http https://engine.catchintent.com/mcp
```

See [setup docs](https://catchintent.com/docs/mcp/setup) for other clients.

## Install

```bash
npx skills add catchintent/skills
```

## Skills

CatchIntent has two product surfaces — **Social Listening** (Reddit/HN/X/Bluesky signals from your listeners) and **LinkedIn Intelligence** (LinkedIn agents that deliver scored leads). Skills are organized accordingly.

### LinkedIn Intelligence

Workflows for the LinkedIn agents + leads pipeline.

| Skill | What it does | Trigger |
|-------|-------------|---------|
| **tune-agent** | Adjust an agent's ICP / keywords / signals based on real lead-quality feedback, then start a fresh run | "Tune my agent", "leads are off", "adjust targeting" |
| **browse-leads** | Daily lead review — what's hot, what to action first | "Show me my leads", "what's hot today", "review the queue" |
| **research-lead** | Deep dive on a single lead — discovery context, pain hypothesis, suggested angle | "Research [name]", "what's the angle on this lead" |
| **triage-leads** | One-at-a-time bulk triage — save / archive / skip until the queue is clear | "Triage my leads", "clear the queue" |
| **enrich-leads** | Free public-web research first; fall back to paid `enrich_linkedin_lead` only when needed | "Enrich [name]", "find email", "fill out the profile" |
| **ab-outreach** | Draft 2-3 distinct variants with different pain angles; user picks the winner | "Draft DMs for [name]", "give me variants", "A/B this" |

### Social Listening

Workflows for the signals + listeners + outreach surface (Reddit / HN / X / Bluesky).

| Skill | What it does | Trigger |
|-------|-------------|---------|
| **morning-signals** | Daily signal review — pulls hot signals, prioritizes, recommends next steps | "Check my signals", "what's new today" |
| **draft-outreach** | Drafts personalized outreach for your best signals in your brand voice | "Draft replies", "write DMs for my signals" |
| **handle-replies** | Classifies prospect replies and drafts contextual follow-ups | "Handle my replies", "what should I say back" |
| **pipeline-review** | Reviews pipeline health — stale signals, follow-ups needed, deals to close | "How's my pipeline", "what needs follow-up" |
| **listener-tune** | Analyzes keyword performance, suggests optimizations | "How are my listeners doing", "optimize keywords" |
| **campaign-retro** | Diagnoses what's broken — targeting, voice, or reply handling | "Why aren't my signals converting", "what's not working" |
| **weekly-report** | Generates a team-ready weekly performance summary | "Weekly report", "how did we do this week" |

### Setup & Positioning

Cross-product workspace configuration.

| Skill | What it does | Trigger |
|-------|-------------|---------|
| **calibrate-voice** | Calibrates brand voice from your real messages or a preset archetype | "Set up my voice", "my responses don't sound like me" |
| **craft-offer** | Builds your product offer — value prop, pain point, mechanism, proof — that powers all AI outreach | "Set up my offer", "my outreach feels generic" |

## Suggested Workflows

**LinkedIn Intel — daily:**
```
/browse-leads → /triage-leads → /research-lead (top saves) → /ab-outreach
```

**LinkedIn Intel — when leads feel off:**
```
/browse-leads → /tune-agent → wait for new run → /browse-leads
```

**Social Listening — daily:**
```
/morning-signals → /draft-outreach → /handle-replies
```

**First-time setup:**
```
/calibrate-voice → /craft-offer → /listener-tune (Social Listening) and/or /tune-agent (LinkedIn Intel)
```

**Weekly optimization:**
```
/weekly-report → /campaign-retro (if something's off) → /listener-tune
```

## How It Works

These skills are playbooks that tell your AI tool how to use CatchIntent's MCP tools effectively. They handle the workflow logic — which tools to call, in what order, and how to present results — so you just describe what you want in plain language.

All skills follow [STANDARDS.md](STANDARDS.md) — shared conventions for data-first analysis, confirm-before-write, and cross-skill references.

## Contributing

See [STANDARDS.md](STANDARDS.md) for the skill authoring conventions and the [agentskills.io spec](https://agentskills.io/specification) for the SKILL.md format.

## Links

- [CatchIntent](https://catchintent.com)
- [MCP Setup Guide](https://catchintent.com/docs/mcp/setup)
- [Available MCP Tools](https://catchintent.com/docs/mcp/tools)
