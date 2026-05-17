# CatchIntent Agent Skills

Pre-built workflows for GTM teams using [CatchIntent](https://catchintent.com) with AI tools like Claude Code, Cursor, and Windsurf.

## Prerequisites

```bash
claude mcp add catchintent --transport http https://engine.catchintent.com/mcp
```

See [setup docs](https://catchintent.com/docs/mcp/setup) for other clients.

## Install

```bash
npx skills add catchintent/skills
```

## Skills

CatchIntent has one unified surface: **agents** create **leads** you triage, research, and act on. Every lead is a person on either **LinkedIn** or **X** — the `sourcePlatform` field on each lead tells you which. These skills cover the discovery side (agents → leads → research). Outreach skills will land once the outreach module ships.

| Skill | What it does | Trigger |
|-------|-------------|---------|
| **daily-triage** | Walk the new-lead queue one at a time — save / archive / skip until clear | "Triage my leads", "what's new", "clear the queue" |
| **research-lead** | Deep-research a single LinkedIn lead — public sources first, paid enrichment only if approved | "Research X", "what's the angle on this lead" |
| **enrich-leads** | Bulk paid enrichment via Apify ($0.002/lead, idempotent) for LinkedIn leads | "Enrich these leads", "fill in profile data" |
| **tune-agent** | Diagnose an underperforming agent and propose specific targeting changes | "Tune my agent", "leads are off" |
| **pipeline-review** | Pipeline health check — usage, agents, lead funnel, queue; monthly mode adds a lead-quality retro | "How's my pipeline", "weekly review", "monthly retro" |
| **craft-offer** | Sharpen the structured offer (pain point, mechanism, proof) that powers AI lead scoring | "Fix my offer", "what's my one-liner" |

## Suggested Workflows

**Daily:**
```
/daily-triage → /research-lead (on the high-priority saved leads) → /enrich-leads (LinkedIn leads missing detail)
```

**When leads feel off:**
```
/daily-triage → /tune-agent → wait 3-5 days → /daily-triage
```

**First-time setup:**
```
/craft-offer → create your first agent → /daily-triage
```

**Weekly / monthly:**
```
/pipeline-review → /tune-agent (on the worst agent)
```

## How It Works

Skills are playbooks that tell your AI tool how to compose CatchIntent's MCP tools (`agent_*`, `lead_*`, `engagement_*`, `product_*`, `workspace_*`, `denylist_*`) into useful workflows. The tool surface is unified — there are no SL- or LI-specific tool names. Where lead shape matters, tools take a `kind` discriminator.

All skills follow [STANDARDS.md](STANDARDS.md) — shared conventions for data-first analysis, confirm-before-write, and cross-skill references.

## Contributing

See [STANDARDS.md](STANDARDS.md) for skill authoring conventions and the [agentskills.io spec](https://agentskills.io/specification) for the SKILL.md format.

## Links

- [CatchIntent](https://catchintent.com)
- [MCP Setup Guide](https://catchintent.com/docs/mcp/setup)
- [Available MCP Tools](https://catchintent.com/docs/mcp/tools)
