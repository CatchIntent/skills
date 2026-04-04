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

### LinkedIn Prospecting

| Skill | What it does | Trigger |
|-------|-------------|---------|
| **find-prospects** | Search LinkedIn for ICP-matching prospects with behavioral intelligence scoring | "Find me leads", "Search LinkedIn for VPs of Sales" |
| **research-competitor** | Full competitive intel — content strategy, frustrated commenters, audience capture, ad analysis | "Research [competitor]", "Steal [competitor]'s audience" |
| **find-buyers** | Find people showing active buying intent — commenters, group discussions, hiring signals | "Find buying signals for [topic]", "Who's looking for a solution like ours" |
| **research-prospect** | Deep behavioral intelligence on a specific person before outreach | "Research this person", "What does [name] care about" |
| **analyze-post** | Extract high-intent commenters from any LinkedIn post URL | "Analyze this post", "Find prospects from this thread" |

### Signal Workflows

| Skill | What it does | Trigger |
|-------|-------------|---------|
| **morning-signals** | Daily signal review — pulls hot signals, prioritizes, recommends next steps | "Check my signals", "What's new today" |
| **draft-outreach** | Drafts personalized outreach for your best signals in your brand voice | "Draft replies", "Write DMs for my signals" |
| **handle-replies** | Classifies prospect replies and drafts contextual follow-ups | "Handle my replies", "What should I say back" |
| **enrich-prospects** | Finds unenriched prospects, triggers enrichment, exports lists | "Get me emails", "Enrich my prospects" |

### Analytics & Optimization

| Skill | What it does | Trigger |
|-------|-------------|---------|
| **pipeline-review** | Reviews pipeline health — stale signals, follow-ups needed, deals to close | "How's my pipeline", "What needs follow-up" |
| **listener-tune** | Analyzes keyword performance, suggests optimizations | "How are my listeners doing", "Optimize keywords" |
| **campaign-retro** | Diagnoses what's broken — targeting, voice, or reply handling — with data | "Why aren't my signals converting", "What's not working" |
| **weekly-report** | Generates a team-ready weekly performance summary | "Weekly report", "How did we do this week" |

### Setup

| Skill | What it does | Trigger |
|-------|-------------|---------|
| **calibrate-voice** | Calibrates brand voice from your real messages or a preset archetype | "Set up my voice", "My responses don't sound like me" |

## Suggested Workflows

**Daily prospecting loop:**
```
/morning-signals → /draft-outreach → /handle-replies
```

**LinkedIn research flow:**
```
/find-buyers → /research-prospect (top leads) → draft outreach
```

**Competitive intelligence:**
```
/research-competitor → /analyze-post (their viral posts) → /find-prospects (target their audience)
```

**Weekly optimization:**
```
/weekly-report → /campaign-retro (if something's off) → /listener-tune (fix targeting)
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
