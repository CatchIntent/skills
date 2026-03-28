---
name: weekly-report
description: Generate a weekly performance report for your team. Summarizes signals, wins, pipeline health, and listener performance. Use when asking for a "weekly report", "weekly summary", or "how did we do this week".
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

4. **People activity** — Use `search_people` to summarize:
   - New prospects discovered this week
   - People enriched
   - ICP match distribution (strong/moderate/weak)

5. **Listener health** — Use `list_listeners` to briefly note:
   - Which listeners produced the most signals
   - Any listeners with concerning health metrics

6. **Content filters** — Use `get_platform_denylist` to note active filters (blocked subreddits/users). If none are set and listener health shows noise from specific sources, suggest adding filters.

7. **Usage** — Use `get_usage` to show plan consumption.

## Output Format

Structure the report as:

```
## CatchIntent Weekly Report — [Date Range]

### Highlights
- [Top 3 wins/achievements]

### Signals
- [Table: platform, count, top intent]

### Pipeline
- [Funnel: new → reached_out → replied → won]

### People
- [New prospects, enriched, ICP breakdown]

### Listener Health
- [Brief status per listener]

### Usage
- [Signals: X/Y used, Z% of plan]
```

Keep it to one screen. Executives should be able to read this in 30 seconds.
