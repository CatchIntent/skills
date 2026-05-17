---
name: enrich-leads
description: Bulk paid enrichment for LinkedIn leads via Apify. Costs ~$0.002 per lead, one-shot. Use when the user says "enrich these leads", "fill in profile data", or before pushing a batch to CRM that needs full profiles.
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Enrich Leads

You are running paid profile enrichment on a batch of LinkedIn leads. Always confirm cost + scope before spending.

## What To Do

1. **Identify the batch** — User passes leadIds, or describe the batch. If unclear:
   - `lead_search` with `sourcePlatform: 'linkedin'`, `status: ['saved']` (the natural pile to deepen), page size 50. Apify enrichment is LinkedIn-only — X leads already have bio + recent posts from the agent run.
   - Show count + warmth breakdown

2. **Filter to un-enriched** — For each lead, check `fullEnrichmentAt`. Drop already-enriched leads from the batch — `lead_enrich` is idempotent but charges whether or not new data is found, so don't re-spend.

3. **Show the cost** — Display:
   - N leads to enrich (excluded M already-enriched)
   - Estimated cost (~$0.002 × N)
   - What gets pulled: experience, education, skills, follower count, email if available
   - Warning that cost is incurred whether or not data is returned per lead

4. **Confirm** — Wait for explicit "go". Don't enrich on assumption.

5. **Run sequentially** — For each leadId, call `lead_enrich`. Show progress every 5: "5/12 done, 3 with email, 2 partial."

6. **Summarize** — Final report:
   - Enriched: N (with full profile)
   - Partial: M (some fields missing)
   - Failed: L (no data — still charged)
   - Emails surfaced: K
   - Total cost: ~$X
   - Suggest next: `/research-lead` if any high-priority leads need deeper digging before you act on them

## Guidelines

- **Free before paid.** If the goal is to deepen ONE high-priority lead, point them at `/research-lead` first — public sources are usually enough.
- **Idempotent.** Don't double-charge. Check `fullEnrichmentAt` before every call.
- **Don't enrich cool leads.** Cool warmth tier rarely converts; spending money on enrichment is wasteful. Suggest filtering to hot+warm only.
- **Confirm scope every time.** Even if the user said "enrich saved leads" yesterday, confirm before spending today.
