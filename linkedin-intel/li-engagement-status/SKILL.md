---
name: li-engagement-status
description: Operator-level LinkedIn outreach status report — for one user (or each member of the workspace). Pulls active engagements, replies received, paused/exited counts, and the inbox so you can see what's in flight, what needs attention, and where things are stuck. Use when the user says "how's my outreach", "engagement status for [person]", "what's in flight", "team outreach report", or "weekly outreach summary".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Engagement Status Report

You are summarising the state of LinkedIn outreach engagements for one operator (the user, a teammate, or the whole workspace) so they can see what's working, what needs attention, and what's stalled.

## What To Do

1. **Resolve scope** — Ask: "Status for you, a specific teammate, or the whole workspace?" Default to the current user if not specified. If a teammate, take their `ownerUserId` (from `li_inbox_list` results or directly).

2. **Resolve product context** — `workspace_product_list` — pick the product if there are multiple. The unified inbox is product-scoped.

3. **Pull the data in parallel:**
   - `li_engagement_list` with `ownerUserId` (if scoped) — gives the full state breakdown across all states for this owner.
   - `li_inbox_list` with `productId`, `ownerUserId`, `liveOnly: true`, and `hasReplies: true` — these are the live conversations *waiting on the operator*.
   - `li_inbox_list` again with `liveOnly: true`, no `hasReplies` filter — all live engagements for context.

4. **Categorize and summarise** — group by state:
   - **Active** — touches still being sent. Count + how many have any reply yet.
   - **Replied** — these need a human follow-up. List each by name, latest reply preview, days since reply. **Highlight the oldest** — replies stale > 48h are the urgent risk.
   - **Paused** — count + reason if visible. Note any with `pausedUntil` in the past (auto-resume overdue).
   - **Exited (won/opt-out/manual/completed)** — count by reason for the period (last 30d).

5. **Surface the action queue clearly:**
   - **Reply backlog** — engagements in REPLIED state with no follow-up touch yet, sorted by oldest first. The user should hit these today.
   - **Stuck active** — engagements active for 14+ days with no reply. Candidates for /li-reassign-stale or exit.
   - **Connection wall** — engagements where the connect request was sent but never accepted (no `connectionAcceptedAt` after 7+ days).

6. **For workspace-wide reports**, add a per-operator breakdown: name, active count, reply backlog, exit reasons. This is the GTM lead's view.

## What NOT To Do

- Don't dump every engagement into one giant table. Prioritize the action queue.
- Don't suggest specific replies — that's the job of `/li-ab-outreach` or `/sl-handle-replies`. This skill is the dashboard, not the action.
- Don't include exited engagements in the live count — they're terminal and bloat the picture.

## Suggested Next Steps

If you find:
- **Reply backlog** → "Open the inbox in the dashboard, or paste the lead name and I'll draft a follow-up via `/li-ab-outreach`."
- **Stuck active engagements** → "Want me to help triage these via `/li-reassign-stale`?"
- **An operator with a heavy backlog** → "Want to redistribute? `/li-reassign-stale` can move stale ones to another teammate."
