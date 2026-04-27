# Skill Standards

All CatchIntent skills follow these conventions. Read this before writing or modifying any skill.

## Frontmatter (agentskills.io spec)

Every `SKILL.md` must have:

```yaml
---
name: skill-name          # lowercase + hyphens, must match directory name
description: ...          # under 1024 chars, describes what AND when to use
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---
```

## Interaction Protocol

### Data Before Opinions
Always fetch and show real data before making recommendations. Never suggest changes based on assumptions — cite specific numbers from tool results.

### Confirm Before Writing
Before calling any tool that modifies state (`update_product_info`, `update_listener`, `create_listener`, `toggle_listener`, `update_signal_status`, `update_platform_denylist`, `create_linkedin_agent`, `update_linkedin_agent`, `set_linkedin_agent_status`, `update_linkedin_lead_status`, `enrich_linkedin_lead`, `push_to_crm`), show what you're about to change and wait for explicit user approval.

Read-only tools (`get_*`, `search_*`, `list_*`) can be called without confirmation.

### One Thing at a Time
When processing multiple items (signals, replies, listeners), handle each fully before moving to the next. Don't batch approvals — let the user confirm or adjust each one.

### Show Your Reasoning
When classifying, scoring, or recommending, explain why. Quote specific data: "Keyword 'project management' has 0.3% signal rate across 847 matches — that's noise." Don't just label things.

## Tool Usage Patterns

### Always Start With Context
Most skills should begin with `get_product_info` to understand the product, competitors, ICP, and brand voice. If the workspace has multiple products, call `list_products` first and ask which one — never silently pick. This context shapes everything: prioritization, language, recommendations.

### Check Plan Limits
Before suggesting features or actions, use `get_usage` to check what the user's plan supports. Never recommend features they can't access. Never hardcode plan limits — always read them from the tool response.

### Funnel Awareness
When analyzing performance, use `get_workspace_stats` which returns a `funnel` object with conversion rates:
- `actionRate` — % of signals acted on (reached_out / total)
- `replyRate` — % of outreach that got replies (replied / reached_out)
- `winRate` — % of replies that converted (won / replied)

Use these rates to diagnose bottlenecks rather than raw counts alone.

### First-Touch Outreach
When generating DMs via `generate_response`, pass `firstTouch: true` (the default) for initial outreach. This enforces: no product pitch, lead with the signal, value-first. Only set `firstTouch: false` for follow-up messages where product mention is appropriate.

### Platform Denylist
If you notice recurring low-quality sources (subreddits, users), proactively suggest blocking them via `update_platform_denylist`. Mention this is free and immediate.

## Output Style

- **Scannable.** Tables for lists, bullet points for recommendations. No walls of text.
- **Numbers first.** Lead with data, then interpretation.
- **Actionable.** End with specific next steps, not vague advice. Link to other skills when relevant (e.g., "Run `/listener-tune` to fix keyword targeting").
- **Honest about limits.** If there isn't enough data to draw conclusions, say so. "With only 5 outreach attempts, reply rate isn't meaningful yet" is better than a false diagnosis.

## LinkedIn Intelligence Patterns

Skills that touch LinkedIn agents or leads (`list_linkedin_agents`, `get_linkedin_agent`, `create_linkedin_agent`, `update_linkedin_agent`, `set_linkedin_agent_status`, `get_linkedin_agent_runs`, `list_linkedin_leads`, `get_linkedin_lead`, `update_linkedin_lead_status`, `enrich_linkedin_lead`, `draft_linkedin_outreach`) must follow these patterns:

### Surface Warmth Tiers, Not Scores
Lead `warmthScore` is a 0–100 integer internally. Skills must surface the tier (`hot` ≥ 70, `warm` 40–69, `cool` < 40 / null) — never the raw number. Filtering accepts tier names; display uses tier names.

### Strict ICP Labels
ICP fields (`locations`, `industries`, `seniorityLevels`, `jobFunctions`, `companySizes`) on agents and products accept **only labels listed in `get_icp_options`**. Always call `get_icp_options` before suggesting a label that wasn't already on file. Unknown labels are rejected — there are no silent drops.

### Prefer Free Enrichment Before Paid
`enrich_linkedin_lead` calls Apify (≈ $0.002, one-shot, charged whether or not data is found). Before invoking it, try free public web sources (LinkedIn URL, company about page, X, GitHub, podcast/blog mentions) via `WebFetch` / `WebSearch`. Only escalate to paid when the user approves and free sources didn't yield enough to write outreach.

### Idempotent Enrichment
`enrich_linkedin_lead` is one-shot — `fullEnrichmentAt` marks "we already tried", not "we succeeded". Don't propose retrying a lead that has the timestamp set.

### Drafting Is Read-Only
`draft_linkedin_outreach` does not write or send. It returns body + rationale + variants. Sending happens externally (extension, Lemlist, manual DM). Never set a lead's status to `pushed` from a draft skill — that status is reserved for actual outreach-tool handoff.

### Stay Inside LinkedIn Intel
LinkedIn skills should not pull or cross-reference social-listening signals. Keep the surfaces separate — the user shifts contexts deliberately.

## Social Listening Patterns

### Signal-First Personalization
When drafting outreach via `generate_response`, always lead with the signal — the specific post or comment that surfaced this person. The signal IS the personalization hook.

### First-Touch Outreach
Pass `firstTouch: true` (the default) for initial outreach. This enforces no product pitch, lead with the signal, value-first. Only set `firstTouch: false` for follow-up messages where product mention is appropriate.

### Platform Denylist
If you notice recurring low-quality sources (subreddits, users), proactively suggest blocking them via `update_platform_denylist`. It is free, immediate, and applies to all listeners.

## Cross-Skill References

Skills should reference each other when relevant:

| Situation | Suggest |
|-----------|---------|
| Brand voice empty or generic | `/calibrate-voice` |
| Product offer vague | `/craft-offer` |
| Keywords underperforming | `/listener-tune` |
| Signals piling up unactioned | `/draft-outreach` |
| Replies sitting unprocessed | `/handle-replies` |
| Low conversion rates | `/campaign-retro` |
| Daily LinkedIn lead review | `/browse-leads` |
| LinkedIn lead queue cluttered | `/triage-leads` |
| Agent delivering bad fits | `/tune-agent` |
| Need angle on a specific lead | `/research-lead` |
| Lead missing context for outreach | `/enrich-leads` |
| Drafting LinkedIn DM | `/ab-outreach` |

## Personalization Examples

For Social Listening (the signal IS the hook):
- Bad: "I noticed you're interested in sales automation"
- Good: "Saw your Reddit post about struggling with SDR outreach at scale"

For LinkedIn Intelligence (the lead's discovery context IS the hook):
- Bad: "I came across your profile and thought we could connect"
- Good: "Your post about scaling outbound from 3 reps to 15 — exact problem we help with"
