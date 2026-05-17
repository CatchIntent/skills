---
name: daily-triage
description: Daily review of new leads — LinkedIn prospects + X posters surfaced by your agents. Walks the queue one at a time, save / archive / draft / skip until clear. Use when starting your day, when the user says "what's new", "triage", "go through my leads", or "clear the queue".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "3.0"
---

# Daily Triage

You are running a focused triage session with a GTM operator. Goal: walk the new-lead queue, end with the queue cleared. No batch approvals, no spam.

## What To Do

1. **Scope** — `product_list`. If multiple products, ask which one. Optionally narrow by `sourcePlatform: 'linkedin' | 'x'` if the user wants to focus on one surface — default is both.

2. **Get brand context** — `product_get` for the chosen product. Note the offer, ICP, competitor list. This is what makes "match vs noise" calls fast.

3. **Pull the queue** — `lead_search` with `productId`, `status: ['new']`, `warmth: ['hot', 'warm']` (cool is rarely worth manual triage), page size 25. Tell the user the count: "8 hot, 6 warm — 14 leads. Let's go."

4. **One at a time** — Hot first, then warm. For each lead:
   - Show: name, title @ company (or X handle for X leads), warmth tier, source platform, `reasonLine`, `discovery.painHypothesis` if present
   - Ask: "Save / Archive / Skip / Stop?"
   - **Save** → `lead_update_status` with `status: 'saved'`
   - **Archive** → `lead_update_status` with `status: 'archived'`
   - **Skip** → leave as `new`, next
   - **Stop** → end session, summarize

5. **Progress pulse** — Every 5 leads: "5/14 done — 2 saved, 1 archived." Keeps momentum.

6. **Wrap up** — On Stop or queue-empty, summarize:
   - Triaged this session, by outcome
   - Names of saved leads (the to-action pile)
   - Skipped (suggest tomorrow)
   - Suggest `/research-lead` on the highest-priority saved leads, or `/enrich-leads` for any saved LinkedIn leads missing detail

## Guidelines

- **Never auto-decide.** Even on a clearly-bad lead, ask. The user is calibrating their agent's quality — they need to see the misses.
- **Don't enrich during triage.** Expensive + slow. Point them at `/enrich-leads` after.
- **Surface the hook.** The painHypothesis or reasonLine is what lets the user decide in 5 seconds. Show that, not the whole profile.
- **Source platform context.** `sourcePlatform` (`linkedin` | `x`) tells you where the lead was discovered — surface it so the user knows the channel, but triage is the same: save the good ones, archive the misses.
- If queue is empty, congratulate and stop. Don't manufacture work.
