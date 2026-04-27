---
name: browse-leads
description: Daily review of LinkedIn leads delivered by your agents. Filters by warmth tier (hot/warm/cool) and status, surfaces what to act on first, and chains naturally into research, enrichment, or outreach. Use when the user says "show me my leads", "what's hot today", "any new leads", "review the queue", or "what should I work on".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Browse Leads

You are helping a GTM engineer review the LinkedIn leads their agents delivered. The point of this skill is **prioritization** — not a giant data dump.

## What To Do

1. **Resolve product context** — If the user has multiple products (`list_products`), ask which one. Otherwise default to the only product. All `list_linkedin_leads` calls require a `productId`.

2. **Pull the priority queue** — In one call, get the actionable bucket:
   - `list_linkedin_leads` with `productId`, `warmth: ['hot', 'warm']`, `status: ['new']`, page size 25
   - This is the user's "do today" list.

3. **Show a tight summary, not a wall** — Format as a table with:
   - Name (linked to LinkedIn URL)
   - Title @ Company
   - Warmth tier (hot/warm — derive from `warmthScore` using the >=70 hot, 40-69 warm rule)
   - One-line `reasonLine` from the lead
   - Workflow tag (e.g. "Hiring signal", "Competitor engager")
   Keep it to 25 rows max. If there are more, mention how many in total and offer to page.

4. **Recommend a next step** — Don't just list leads. After the table, call out:
   - Top 3 hot leads with the strongest pain hypothesis (read `discovery.painHypothesis` from `get_linkedin_lead` if needed for the top 3 only)
   - One concrete next action: "Run `/research-lead` on [name] before you DM" or "Run `/triage-leads` to clear the new queue" or "Run `/ab-outreach` to draft variants for [name]"

5. **Offer secondary views on demand** — Don't show these by default; mention them as follow-ups:
   - "Want to see warm leads only?" → `warmth: ['warm']`
   - "Anything saved I haven't acted on?" → `status: ['saved']`
   - "Cool leads by agent?" → `warmth: ['cool']`, `agentId: ...`
   - "Search by company?" → `searchQuery: 'Acme'`
   - "From a specific agent?" → `agentId: ...` (call `list_linkedin_agents` if user names one)

## Guidelines

- **Never expose `warmthScore` numbers.** Surface tier names (Hot, Warm, Cool) — that's the contract. The number is internal.
- **One product at a time.** Don't merge leads across products in the same view; that's confusing.
- **Don't auto-enrich or auto-draft.** Browsing is read-only. Recommendations point to other skills (`/research-lead`, `/enrich-leads`, `/ab-outreach`); the user invokes them.
- **Empty queue is good news.** If `total === 0` for the hot+warm+new bucket, say "Inbox zero on hot and warm leads. Either tune the agent for more volume or check the cool tier."
- If many leads share the same `workflowTag` (e.g. all "Hiring signal"), call that out — it's a hint about which strategy is firing best.
- If the user has no agents at all (`list_linkedin_agents` returns empty), point them at `/tune-agent` (or the dashboard) to create one first.
