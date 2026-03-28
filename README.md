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
| **morning-signals** | Daily signal review — pulls hot signals, prioritizes, recommends next steps | "Check my signals", "What's new today" |
| **draft-outreach** | Drafts personalized outreach for your best signals in your brand voice | "Draft replies", "Write DMs for my signals" |
| **enrich-prospects** | Finds unenriched prospects, triggers enrichment, exports lists | "Get me emails", "Enrich my prospects" |
| **pipeline-review** | Reviews pipeline health — stale signals, follow-ups needed, deals to close | "How's my pipeline", "What needs follow-up" |
| **listener-tune** | Analyzes keyword performance, suggests optimizations | "How are my listeners doing", "Optimize keywords" |
| **weekly-report** | Generates a team-ready weekly performance summary | "Weekly report", "How did we do this week" |

## How It Works

These skills are playbooks that tell your AI tool how to use CatchIntent's 20 MCP tools effectively. They handle the workflow logic — which tools to call, in what order, and how to present results — so you just describe what you want in plain language.

## Links

- [CatchIntent](https://catchintent.com)
- [MCP Setup Guide](https://catchintent.com/docs/mcp/setup)
- [Available MCP Tools](https://catchintent.com/docs/mcp/tools)
