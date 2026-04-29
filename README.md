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

CatchIntent has two product surfaces — **Social Listening** (Reddit / HN / X / Bluesky signals from your listeners) and **LinkedIn Intelligence** (LinkedIn agents + leads + outreach engagements). Skills are organized accordingly:

```
skills/
├── social-listening/   sl-* skills — signals, listeners, outreach replies
├── linkedin-intel/     li-* skills — agents, leads, outreach engagements
└── shared/             cross-surface — voice, offer, brand setup
```

Invoke a skill by its prefixed name (e.g. `/li-browse-leads`, `/sl-morning-signals`).

### LinkedIn Intelligence — `linkedin-intel/`

Workflows for the LinkedIn agents + leads + outreach pipeline.

| Skill | What it does | Trigger |
|-------|-------------|---------|
| **li-tune-agent** | Adjust an agent's ICP / keywords / signals based on real lead-quality feedback, then start a fresh run | "Tune my agent", "leads are off", "adjust targeting" |
| **li-browse-leads** | Daily lead review — what's hot, what to action first | "Show me my leads", "what's hot today", "review the queue" |
| **li-research-lead** | Deep dive on a single lead — discovery context, pain hypothesis, suggested angle | "Research [name]", "what's the angle on this lead" |
| **li-triage-leads** | One-at-a-time bulk triage — save / archive / skip until the queue is clear | "Triage my leads", "clear the queue" |
| **li-enrich-leads** | Free public-web research first; fall back to paid `li_lead_enrich` only when needed | "Enrich [name]", "find email", "fill out the profile" |
| **li-ab-outreach** | Draft 2-3 distinct variants with different pain angles; user picks the winner | "Draft DMs for [name]", "give me variants", "A/B this" |
| **li-engagement-status** | Operator/team outreach status — what's in flight, replies waiting, stuck conversations | "How's my outreach", "team outreach report", "weekly outreach summary" |
| **li-enroll-from-signals** | Bridge: find hot Social Listening signals, match to LinkedIn leads, enroll into outreach | "Turn signals into outreach", "DM the people who posted about us" |
| **li-reassign-stale** | Find stuck engagements and reassign or exit them; redistribute when someone goes on leave | "Find stale engagements", "[person] is on leave, move their leads" |

### Social Listening — `social-listening/`

Workflows for the signals + listeners + reply-handling surface (Reddit / HN / X / Bluesky).

| Skill | What it does | Trigger |
|-------|-------------|---------|
| **sl-morning-signals** | Daily signal review — pulls hot signals, prioritizes, recommends next steps | "Check my signals", "what's new today" |
| **sl-draft-outreach** | Drafts personalized outreach for your best signals in your brand voice | "Draft replies", "write DMs for my signals" |
| **sl-handle-replies** | Classifies prospect replies and drafts contextual follow-ups | "Handle my replies", "what should I say back" |
| **sl-pipeline-review** | Reviews pipeline health — stale signals, follow-ups needed, deals to close | "How's my pipeline", "what needs follow-up" |
| **sl-listener-tune** | Analyzes keyword performance, suggests optimizations | "How are my listeners doing", "optimize keywords" |
| **sl-campaign-retro** | Diagnoses what's broken — targeting, voice, or reply handling | "Why aren't my signals converting", "what's not working" |
| **sl-weekly-report** | Generates a team-ready weekly performance summary | "Weekly report", "how did we do this week" |

### Shared — `shared/`

Cross-surface workspace setup. These power both Social Listening and LinkedIn Intelligence.

| Skill | What it does | Trigger |
|-------|-------------|---------|
| **calibrate-voice** | Calibrates brand voice from your real messages or a preset archetype | "Set up my voice", "my responses don't sound like me" |
| **craft-offer** | Builds your product offer — value prop, pain point, mechanism, proof — that powers all AI outreach | "Set up my offer", "my outreach feels generic" |

## Suggested Workflows

**LinkedIn Intel — daily:**
```
/li-browse-leads → /li-triage-leads → /li-research-lead (top saves) → /li-ab-outreach
```

**LinkedIn Intel — when leads feel off:**
```
/li-browse-leads → /li-tune-agent → wait for new run → /li-browse-leads
```

**Social Listening — daily:**
```
/sl-morning-signals → /sl-draft-outreach → /sl-handle-replies
```

**First-time setup:**
```
/calibrate-voice → /craft-offer → /sl-listener-tune (Social Listening) and/or /li-tune-agent (LinkedIn Intel)
```

**Weekly optimization:**
```
/sl-weekly-report → /sl-campaign-retro (if something's off) → /sl-listener-tune
```

**LinkedIn outreach — operations:**
```
/li-engagement-status → /li-reassign-stale → /li-enroll-from-signals (top up the queue)
```

## How It Works

These skills are playbooks that tell your AI tool how to use CatchIntent's MCP tools effectively. Each tool is namespaced by surface — `sl_*` for Social Listening, `li_*` for LinkedIn Intelligence, `workspace_*` for cross-surface — so you (and your AI) can always tell what a tool affects from its name. Skills handle the workflow logic so you just describe what you want in plain language.

All skills follow [STANDARDS.md](STANDARDS.md) — shared conventions for data-first analysis, confirm-before-write, and cross-skill references.

## Contributing

See [STANDARDS.md](STANDARDS.md) for the skill authoring conventions and the [agentskills.io spec](https://agentskills.io/specification) for the SKILL.md format.

## Links

- [CatchIntent](https://catchintent.com)
- [MCP Setup Guide](https://catchintent.com/docs/mcp/setup)
- [Available MCP Tools](https://catchintent.com/docs/mcp/tools)
