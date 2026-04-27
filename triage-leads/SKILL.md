---
name: triage-leads
description: Interactive bulk triage of new LinkedIn leads — review one at a time, decide save / archive / push (or skip), and clear the queue without context-switching. Built for daily lead-review sessions. Use when the user says "triage my leads", "go through my new leads", "clear the queue", or "let's review what came in".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Triage Leads

You are running a focused triage session with a GTM engineer. The point: walk through the new-lead queue **one lead at a time**, and end the session with the queue cleared. No batch approvals, no spam.

## What To Do

1. **Resolve scope** — Confirm the product (`list_products` if multiple). Ask which warmth tier to triage (default: `['hot', 'warm']` only — cool is rarely worth manual triage). Optionally narrow by `agentId` if the user wants to focus on one agent's output.

2. **Pull the queue** — `list_linkedin_leads` with `productId`, `status: ['new']`, `warmth: <chosen>`, page size 25. Tell the user how many leads are in the queue ("12 hot, 8 warm — let's go through them").

3. **One at a time** — For each lead, in order of warmth (hot first, then warm):
   - Show: name, title @ company, warmth tier, `reasonLine`, and `discovery.painHypothesis` if present (call `get_linkedin_lead` only if the list response doesn't include enough detail; the list endpoint already returns the lead body)
   - Ask: "Save / Archive / Skip / Stop?" — single keystroke if possible
   - **Save** → `update_linkedin_lead_status` to `saved`
   - **Archive** → `update_linkedin_lead_status` to `archived`
   - **Skip** → leave as `new`, move to next (user may come back later)
   - **Stop** → end the session here, summarize what was done

4. **Show progress** — After every 5 leads, give a one-line progress update: "5/20 done — 3 saved, 2 archived." Keeps momentum and lets the user feel the queue shrink.

5. **Wrap up** — At the end (or on Stop), summarize:
   - Total triaged this session
   - Saved count + names (these are the user's "to-action" pile)
   - Archived count
   - Skipped count (if non-zero, suggest re-running tomorrow)
   - Suggest `/ab-outreach` next on the saved leads, or `/enrich-leads` if any saved leads aren't enriched

## Guidelines

- **Never auto-decide.** Even on a clearly-bad lead, ask. The user is building intuition for their agent's quality — they need to see the misses.
- **Don't enrich during triage.** Triage is a fast yes/no/maybe loop. Enrichment costs money and slows the rhythm. If they want it, point them at `/enrich-leads` after.
- **Don't draft messages during triage.** Same reason — drafting belongs in `/ab-outreach` after the queue is cleared.
- **Surface the pain hypothesis if present.** That's the signal that helps the user decide save/archive in 5 seconds.
- **Stay inside LinkedIn Intel.** Don't pull or reference social listening signals during triage; it breaks the rhythm.
- If the queue is empty when you start, congratulate the user and stop — don't manufacture work.
- If the user wants to bulk-save or bulk-archive a chunk ("archive all the cool ones"), call `update_linkedin_lead_status` per lead in a tight sequence and confirm only once before the batch.
