---
name: pipeline-review
description: Review your signal and prospect pipeline. Checks signals stuck without responses, identifies stale outreach, and finds deals to close. Use when asking "how's my pipeline", "what needs follow-up", or "pipeline review".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Pipeline Review

You are helping a GTM professional review and clean up their CatchIntent pipeline.

## What To Do

1. **Get the board view** — Use `get_signals_board` to see signals grouped by status.

2. **Identify action items** in this priority order:

   **Replied signals (close them):**
   - Signals in `replied` status — these people responded, time to close
   - For each: show the signal, suggest marking as `won` or `lost`

   **No-response signals (follow up or close):**
   - Signals in `no_response` status
   - If older than 7 days with no response, suggest marking as `lost`
   - If recent, suggest retrying outreach (move back to `reached_out`)

   **Reached-out signals (check for staleness):**
   - Signals in `reached_out` for a while
   - Suggest moving to `no_response` if enough time has passed

   **New signals piling up:**
   - Count of `new` signals — if > 20, flag that the inbox needs attention

3. **People pipeline** — Use `search_people` to check:
   - People in `contacted` status — any replies?
   - People in `approved` but not yet contacted — ready for outreach?

4. **Summarize** — End with a brief:
   - Total signals by status
   - Signals needing immediate action
   - Win/loss count this period (from `get_workspace_stats`)

## Output Format

Start with the summary, then drill into each action item. Use tables for signal lists, keep recommendations actionable ("Mark as won", "Follow up", "Archive").
