---
name: li-enroll-from-signals
description: Bridge from Social Listening signals to LinkedIn outreach engagements. Finds hot Reddit/HN/X signals, looks up the same people as LinkedIn leads, and enrolls them into the right outreach playbook so the operator's connected LinkedIn account messages them. Use when the user says "turn signals into outreach", "enroll my hot signals", "convert Reddit signals to LinkedIn", "bridge signals to outreach", or "DM the people who posted about us".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Enroll From Signals

You are converting **intent** (Social Listening signals — someone posted about a competitor, asked for an alternative, complained about a pain point) into **action** (LinkedIn outreach engagements where their LinkedIn account is messaged on a playbook). This is the moat skill — bridging the two surfaces only CatchIntent does well.

## What To Do

1. **Find the candidate signals.** Default: hottest unaction-ed signals for the operator.
   - `sl_signal_search` with `status: ['new']`, `warmth: ['hot']`, `sortBy: 'relevanceScore'`, `sortOrder: 'DESC'`, page size 20.
   - If user gave specific signal IDs, use those instead.

2. **Resolve product context** — `workspace_product_list` for `productId`. Outreach engagements are product-scoped.

3. **For each candidate signal**, pull `sl_signal_get` to get the author info (platform, username, profile URL if available). Then look for a matching LinkedIn lead:
   - `li_lead_list` with `productId`, `searchQuery: <author name>` — find a lead match.
   - If multiple leads match, prefer the one whose company / title aligns with the signal context. Show the user the top 1-2 if ambiguous.
   - If no lead match exists, that signal can't be enrolled — note it and skip. (Future: trigger LI agent to find the person; not in scope here.)

4. **Show the proposed enrollments before doing anything** — table:
   - Signal title + platform + intent
   - Matched LinkedIn lead (name, title, company, warmth tier)
   - Suggested touch purpose: `connect_note` if not connected, `first_dm` if already 1st-degree
   - Note if a different operator already has an active engagement (the workspace constraint is one operator per lead — calling enroll will return `LEAD_ALREADY_ENROLLED`).

5. **Confirm before enrolling.** Present the list and ask: "Enroll these N leads into outreach? Each will become an active engagement under your account." Wait for explicit yes.

6. **Enroll one at a time.** For each:
   - `li_engagement_enroll` with `productId`, `leadId`, optional `playbookId` (omit to use the best-match playbook for the lead's strategy type).
   - On success: also update the signal — `sl_signal_update_status` with status `reached_out` (the signal led to outreach now).
   - On `LEAD_ALREADY_ENROLLED` (409): show who owns it and offer `/li-reassign-stale` if it should move to the current user.
   - On any other error: report and continue with the rest.

7. **Summarise the result:**
   - X enrolled successfully
   - Y skipped (already enrolled by someone else — link to the inbox)
   - Z failed (with reason)
   - Suggested next step: "Use `/li-ab-outreach` to draft variants for the strongest of these before the first touch goes out."

## What NOT To Do

- Don't enroll without showing the user the matched-lead table first. A bad name match means messaging the wrong person.
- Don't skip the `sl_signal_update_status` step on success — the signal pipeline tracks this; otherwise the same signal reappears tomorrow.
- Don't auto-pick a playbook silently — if the lead's strategy type has no good match, ask the user to pick or offer to skip.
- Don't trigger paid enrichment (`li_lead_enrich`) here — keep this skill cheap. Enrichment is its own skill.

## Suggested Next Steps

- `/li-ab-outreach <leadId>` — write the first message
- `/li-engagement-status` — see all live engagements
- `/sl-handle-replies` — once the lead replies, switch back to the SL flow if the conversation continues there
