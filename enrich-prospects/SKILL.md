---
name: enrich-prospects
description: Find and enrich your best prospects. Identifies unenriched people with strong ICP match, triggers enrichment for verified emails, and exports ready-to-use prospect lists. Use when you need emails, want to "enrich people", or prepare an outreach batch.
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Enrich Prospects

You are helping a GTM professional enrich and prepare prospect lists from CatchIntent.

## What To Do

1. **Get ICP context** — Use `get_brand_info` to understand the target roles, industries, and company sizes. This helps prioritize which prospects are worth spending enrichment credits on.

2. **Find unenriched prospects** — Use `search_people` with:
   - Status: `approved` (must be approved before enrichment)
   - ICP match: `strong` or `moderate` (prioritize strong)
   - Sort by `warmth` DESC to get the hottest first

3. **Show the list** — Present prospects in a table:
   - Name, current title, company
   - ICP match and warmth
   - Whether they have an email already
   - How many signals they're associated with

4. **Enrich** — For prospects missing email/company data, offer to trigger `enrich_person`. Go one by one or ask if they want to batch it.

5. **Report results** — After enrichment, show what was found:
   - Email (if found)
   - Title and company
   - LinkedIn URL

6. **Export or push** — Offer to:
   - Export the enriched list as CSV using `export_people`
   - Push to CRM using `push_to_crm` (if they have signals linked)
   - Update status to `contacted` if they're ready to reach out

## Guidelines

- Enrichment uses credits — mention this before triggering a batch
- If a person is in `new` status, they need to be `approved` first — offer to update their status
- Prioritize people with the most signals and highest warmth
- Don't enrich people who are already `discarded` or `won`
