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

| Skill | What it does | Trigger |
|-------|-------------|---------|
| **onboard** | Guided first-run setup — analyzes your website, configures ICP, creates first listener | "Set up my workspace", "Get started" |
| **calibrate-voice** | Calibrates brand voice from your real messages or a preset archetype | "Set up my voice", "My responses don't sound like me" |
| **morning-signals** | Daily signal review — pulls hot signals, prioritizes, recommends next steps | "Check my signals", "What's new today" |
| **draft-outreach** | Drafts personalized outreach for your best signals in your brand voice | "Draft replies", "Write DMs for my signals" |
| **handle-replies** | Classifies prospect replies and drafts contextual follow-ups | "Handle my replies", "What should I say back" |
| **enrich-prospects** | Finds unenriched prospects, triggers enrichment, exports lists | "Get me emails", "Enrich my prospects" |
| **pipeline-review** | Reviews pipeline health — stale signals, follow-ups needed, deals to close | "How's my pipeline", "What needs follow-up" |
| **listener-tune** | Analyzes keyword performance, suggests optimizations | "How are my listeners doing", "Optimize keywords" |
| **campaign-retro** | Diagnoses what's broken — targeting, voice, or reply handling — with data | "Why aren't my signals converting", "What's not working" |
| **weekly-report** | Generates a team-ready weekly performance summary | "Weekly report", "How did we do this week" |

## Suggested Workflow

```
/onboard → /calibrate-voice → /morning-signals (daily) → /draft-outreach → /handle-replies → /campaign-retro (weekly)
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
