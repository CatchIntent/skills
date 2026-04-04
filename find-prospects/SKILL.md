---
name: find-prospects
description: Search LinkedIn for prospects matching your ICP. Uses behavioral intelligence to score and rank results beyond just title/industry. Use when you want to "find leads", "search LinkedIn", "build a prospect list", or "find people to reach out to".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Find Prospects

You are helping a GTM professional find LinkedIn prospects matching their ideal customer profile. This is CatchIntent's core prospecting workflow — differentiated by behavioral intelligence scoring that goes beyond title/industry matching.

## What To Do

### Step 1: Establish Context

1. **Check usage and credits** — Use `get_usage` to check plan limits and agentic search credit balance (`agenticSearchCredits.balance`). This tool requires a minimum of 25 credits (1 per person imported). If balance is low, warn the user before proceeding.
2. **Get brand context** — Use `get_brand_info` to load ICP, product info, and competitors. This determines default search filters.

### Step 2: Build Search Criteria

If the user describes who they want ("VP Sales at mid-market SaaS companies in the US"), map their request to search filters:

- **Job titles** → `currentJobTitles` (e.g., ["VP Sales", "Head of Sales", "Director of Sales"])
- **Location** → Use `lookup_filters` with `filterType: "geographies"` to resolve geo IDs, then pass as `geoIds`
- **Industry** → Use `lookup_filters` with `filterType: "industries"` to resolve industry IDs, then pass as `industryIds`
- **Seniority** → Use `lookup_filters` with `filterType: "seniority"` to resolve seniority IDs, then pass as `seniorityLevelIds`
- **Company size** → Use `lookup_filters` with `filterType: "companySize"` to resolve size codes, then pass as `companyHeadcount`
- **Specific companies** → `currentCompanies` or `pastCompanies`
- **Recently changed jobs** → `recentlyChangedJobs: true` if user mentions "new roles", "just started", "recently joined"
- **Exclusions** → Map "not at [company]" to `excludeCurrentCompanies`, "not [title]" to `excludeCurrentJobTitles`

If the user doesn't specify criteria and ICP is configured, derive filters from the saved ICP (targetRoles → titles, geoIds, industryIds, seniorityLevelIds, companyHeadcountIds from brand info).

If ICP is not configured, ask the user to describe their target audience. Suggest updating their ICP in workspace settings so future searches use it automatically.

### Step 3: Confirm and Execute

Before running the search, show the user what you're about to search for:

```
Search criteria:
- Titles: VP Sales, Head of Sales
- Location: United States
- Industry: Software, SaaS
- Company size: 51-200, 201-500
- Estimated cost: 25 credits (1 per person)
```

Wait for confirmation, then run `execute_search` with the filters and `maxResults` (default 25 unless user requests more).

### Step 4: Present Results

Show results in a scannable format:

| # | Name | Title | Company | Warmth | ICP Match |
|---|------|-------|---------|--------|-----------|
| 1 | ... | ... | ... | Hot | Strong |

Include summary stats:
- Total imported: X
- Warmth breakdown: X hot, Y warm, Z cool
- Already tracked (skipped): X

### Step 5: Suggest Follow-ups

Based on results, offer natural next steps:

- **"Want me to deep-dive the top prospects?"** → `/research-prospect` (2 credits each) for behavioral intelligence on specific people
- **"Should I cross-reference with your signals?"** → `search_signals` to find if any of these prospects' companies appeared in Reddit/HN/X discussions
- **"Want to find more from a specific company?"** → Run again with `currentCompanies` filter
- **"Ready to reach out?"** → Use `search_people` to pull the imported prospects, then draft outreach

## Guidelines

- Always check credits before searching — hard minimum is 25
- Use `lookup_filters` to resolve IDs — never hardcode filter values
- If the user has ICP configured, default to those filters but let them override
- Present search criteria for confirmation before executing — this costs credits
- The behavioral intelligence scoring happens automatically during import — mention this as a differentiator ("These aren't just title matches — each prospect is scored by their actual LinkedIn engagement patterns")
- If results are sparse, suggest broadening: wider geo, adjacent titles, larger company sizes
- If results are noisy, suggest narrowing: add industry filters, seniority levels, or exclusions
- Don't run multiple searches without checking credit impact — each search costs 25+ credits
