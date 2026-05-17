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

## Tool Surface

CatchIntent's MCP tools are unified. The full surface:

- **Agents:** `agent_list`, `agent_get`, `agent_create`, `agent_update`, `agent_set_status`, `agent_runs`
- **Leads:** `lead_search`, `lead_get`, `lead_update_status`, `lead_draft_outreach`, `lead_enrich`, `lead_push_to_crm`
- **Outreach:** `engagement_list`, `engagement_get`, `engagement_enroll`, `engagement_reassign`, `engagement_pause`, `engagement_resume`, `engagement_mark_replied`, `engagement_exit`, `inbox_list`
- **Workspace:** `workspace_usage`, `workspace_icp_options`
- **Products:** `product_list`, `product_get`, `product_create`, `product_update`, `product_delete`
- **Denylist:** `denylist_get`, `denylist_update`

> The outreach tools (`lead_draft_outreach`, `engagement_*`, `inbox_list`) exist on the MCP but **no shipped skill uses them yet** — the outreach module is not customer-available. Don't author outreach skills against these until it ships.

### Source Platforms

Every lead is a person on either **LinkedIn** or **X**. The `sourcePlatform` field on each lead tells you which surface they were discovered on. Filter `lead_search` by `sourcePlatform: 'linkedin' | 'x'` when needed.

- `lead_enrich` (Apify, ~$0.002) is LinkedIn-only — X leads already include bio + recent posts at agent-run time.

## Interaction Protocol

### Data Before Opinions
Always fetch and show real data before making recommendations. Cite specific numbers from tool results — never vibes.

### Confirm Before Writing
Before any state-modifying tool (`agent_create`, `agent_update`, `agent_set_status`, `lead_update_status`, `lead_enrich`, `lead_push_to_crm`, `engagement_*` writes, `denylist_update`, `product_*` writes), show the proposed change and wait for explicit approval.

Read-only tools (`*_list`, `*_get`, `lead_search`, `lead_draft_outreach`) can be called without confirmation.

### One Thing at a Time
When processing multiple items, handle each fully before moving to the next. No batch approvals — let the user confirm each.

### Show Your Reasoning
Quote specific data: "12 of last 30 archived leads were in 'Staffing and Recruiting' industry — you don't sell to staffing agencies." Don't just label things.

## Tool Usage Patterns

### Always Start With Context
Most skills should begin with `product_get` to understand offer, ICP, voice. If multi-product, `product_list` first and ask — never silently pick.

### Check Plan Limits
`workspace_usage` returns the active plan limits + funnel stats + delivery counts. Read from there; never hardcode plan tiers.

### Funnel Awareness
The `funnel` block has actionRate, replyRate, winRate. Use these to diagnose bottlenecks.

### Strict ICP Labels
ICP fields (`locations`, `industries`, `seniorityLevels`, `jobFunctions`, `companySizes`) on agents and products accept **only labels listed in `workspace_icp_options`**. Always check before suggesting one — unknown labels are rejected, not silently dropped.

### Free Enrichment Before Paid
`lead_enrich` calls Apify (≈$0.002, charged whether or not data is found). Try `WebFetch` / `WebSearch` on public profiles first — only escalate to paid when the user approves and free sources fell short.

### Idempotent Enrichment
`lead_enrich` is one-shot — `fullEnrichmentAt` marks "we already tried." Don't propose retrying a lead with the timestamp set.

### Platform Denylist
The denylist applies to keyword-discussion agents (LinkedIn + X). If a recurring low-quality account or domain shows up, suggest blocking via `denylist_update`.

## Output Style

- **Scannable.** Tables for lists, bullets for recommendations. No walls of text.
- **Numbers first.** Lead with data, then interpretation.
- **Actionable.** End with specific next steps and link to other skills.
- **Honest about thin data.** With <10 delivered leads or an agent <14 days old, rates aren't meaningful — say so.

## Cross-Skill References

| Situation | Suggest |
|-----------|---------|
| Product offer vague | `/craft-offer` |
| Agent delivering bad fits | `/tune-agent` |
| Lead queue piling up | `/daily-triage` |
| Need an angle on a specific lead | `/research-lead` |
| Pipeline health check / monthly retro | `/pipeline-review` |
| Bulk LinkedIn profile enrichment | `/enrich-leads` |
