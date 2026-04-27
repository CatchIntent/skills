---
name: weekly-report
description: Generate a weekly performance report for your team. Summarizes signals, wins, pipeline health, and listener performance. Use when asking for a "weekly report", "weekly summary", or "how did we do this week".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Weekly Report

You are generating a weekly performance report for a GTM team using CatchIntent.

## What To Do

1. **Get dashboard stats** — Use `get_workspace_stats` with period `week` to get:
   - Signals to review, high intent count
   - Posts analyzed, noise filtered, hours saved
   - Won deals and deal values
   - **Funnel conversion rates** — `funnel.actionRate` (signals acted on), `funnel.replyRate` (outreach → replies), `funnel.winRate` (replies → wins). Show these prominently — they tell the story of the week.

2. **Signal breakdown** — Use `search_signals` for this week's signals to analyze:
   - Total new signals this week
   - Breakdown by platform (Reddit, X, HN, etc.)
   - Breakdown by intent type (alternative, comparison, pain, etc.)
   - Hottest signals (warmth = hot)

3. **Pipeline progress** — Use `get_signals_board` to show:
   - How many moved from new → reached_out
   - How many got replies
   - Won and lost this week
   - Total pipeline value

4. **Listener health** — Use `list_listeners` to briefly note:
   - Which listeners produced the most signals
   - Any listeners with concerning health metrics

5. **Content filters** — Use `get_platform_denylist` to note active filters (blocked subreddits/users). If none are set and listener health shows noise from specific sources, suggest adding filters.

6. **Usage** — Use `get_usage` to show plan consumption.

## Output Format

Structure the report as:

```
## CatchIntent Weekly Report — [Date Range]

### Highlights
- [Top 3 wins/achievements]

### Signals
- [Table: platform, count, top intent]

### Pipeline & Conversion
- [Funnel: new → reached_out → replied → won with counts]
- [Action Rate: X% | Reply Rate: X% | Win Rate: X%]
- [Compare to previous week if available — improving or declining?]

### People
- [New prospects, enriched, ICP breakdown]

### Listener Health
- [Brief status per listener]

### Usage
- [Signals: X/Y used, Z% of plan]
```

Keep it to one screen. Executives should be able to read this in 30 seconds.

If funnel rates show a clear bottleneck (e.g., action rate < 30%, reply rate < 10%, win rate < 20%), flag it and suggest `/campaign-retro` for a deeper diagnosis.
