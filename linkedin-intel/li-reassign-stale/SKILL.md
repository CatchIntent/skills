---
name: li-reassign-stale
description: Find LinkedIn outreach engagements that are stuck (no reply, old, owner unavailable) and either reassign them to another operator or exit them. Surfaces the long tail of engagements eating workspace capacity without producing outcomes. Use when the user says "find stale engagements", "what's stuck", "reassign engagements", "clean up outreach", "redistribute leads", or "[person] is on leave, move their leads".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Reassign Stale Engagements

You are helping a GTM lead clean up the engagement queue — finding live engagements that are not making progress and deciding whether to **reassign** them to another operator or **exit** them.

## What To Do

1. **Resolve scope:**
   - Ask: "Are we redistributing because someone is on leave/quit (move all of theirs), or pruning the workspace's stale long tail?"
   - For per-operator: take the `ownerUserId` whose engagements should move.
   - For workspace cleanup: scan everyone.

2. **Resolve product** — `workspace_product_list`. The inbox is product-scoped; ask if multiple.

3. **Pull the candidates:**
   - `li_inbox_list` with `productId`, `liveOnly: true`, and (if per-operator) `ownerUserId`. Get up to 200 rows.
   - For each row, compute "age in stage":
     - Active state: days since `enrolledAt` with no reply.
     - Paused state: days since pause + check `pausedUntil` (overdue auto-resume?).
     - Replied state: days since last reply with no follow-up touch.

4. **Classify each candidate** into one of:
   - **Stuck active** — active, 14+ days, no reply, no connection acceptance. Candidate to **exit** (reason: `manual`, note: "no engagement after N days").
   - **Connection wall** — active, sent connect, never accepted, 21+ days. Candidate to **exit** (reason: `manual`).
   - **Reassign target** — active or replied, owner is the leaving operator. Candidate to **reassign** to a chosen new owner.
   - **Pause overdue** — paused with `pausedUntil` in the past. Candidate to **resume**.
   - **Healthy** — recent activity. Skip.

5. **Show the categorized table.** For each stale engagement:
   - Lead name + company + title
   - State + days in state
   - Owner (current)
   - Recommended action

6. **For the reassignment path:**
   - Ask who the new owner is. Confirm they have a connected outreach account (the reassign call will fail otherwise — surface that error clearly so the user sets up the account first).
   - Group reassignments by new owner, get explicit confirmation per batch.
   - For each: `li_engagement_reassign` with `engagementId` and `newOwnerUserId`. On error (e.g. target has no active outreach account), stop and report so the user can fix.

7. **For the exit path:**
   - Show the list and confirm the reason once for the batch ("Exit these 8 with reason `manual` and note 'no engagement after 14 days'?").
   - For each: `li_engagement_exit` with `engagementId`, `reason: 'manual'`, `note`.

8. **For pause overdue:**
   - Auto-resume in batch — `li_engagement_resume` for each. Quick, no per-item confirmation.

9. **Summarise:**
   - X reassigned to <new owner>
   - Y exited (`manual`)
   - Z resumed
   - W skipped (errors with reasons)

## What NOT To Do

- **Never reassign or exit silently.** This touches another operator's work — show the table, get explicit confirm.
- Don't reassign to a user without a connected outreach account. Catch the error and prompt them to connect first.
- Don't include engagements that are already exited or completed — `liveOnly: true` filters those out, keep that filter.
- Don't suggest exiting a recently replied engagement — that's a hot conversation, not stale.

## Suggested Next Steps

- After redistribution: `/li-engagement-status <new owner>` to see what they just inherited.
- After exit: `/li-browse-leads` for fresh leads to fill the freed slots.
- If the same operator keeps accumulating stale engagements: that's a coaching signal, not just an inbox problem.
