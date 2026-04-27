---
name: tune-agent
description: Adjust a LinkedIn Intelligence agent's targeting — ICP filters, keywords, competitors, signal types — based on real lead-quality feedback, then start a fresh run and compare warmth distribution. Use when the user says "tune my agent", "my leads are off", "adjust targeting", "agent is delivering bad fits", or "test new ICP".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Tune Agent

You are helping a GTM engineer tune a LinkedIn Intelligence agent based on the actual leads it's delivering. Tuning is a feedback loop — never guess; pull real data first.

## What To Do

1. **Identify the agent** — If the user names one, use it. Otherwise call `list_linkedin_agents` for the active product (ask if they have multiple) and show the agents with their lead count, status, and last run timestamp. Ask which to tune.

2. **Pull current config + recent leads** — In parallel:
   - `get_linkedin_agent` for the full config (description, keywords, competitors, influencers, replacesRoles, icp, signalTypes, status)
   - `list_linkedin_leads` filtered by `agentId` and `warmth: ['cool']`, page size 20 — these are the misses
   - `list_linkedin_leads` filtered by `agentId` and `warmth: ['hot', 'warm']`, page size 20 — these are the hits

3. **Diagnose** — Compare hits vs. misses. Look for patterns:
   - Are cool leads concentrated in certain industries / locations / titles? Tighten the ICP.
   - Are hot leads from a strategy the agent under-weights? Adjust `signalTypes`.
   - Are competitors / keywords pulling noise (e.g. a generic keyword matching the wrong domain)? Drop or replace.
   - Is there a job-title pattern in cool leads that should go in `excludePatterns`?
   Quote actual lead names + companies from the data — never make up examples.

4. **Confirm ICP labels before writing** — If you propose adding/removing ICP fields (locations, industries, seniorityLevels, jobFunctions, companySizes), call `get_icp_options` first and show the **exact label** you'll send. Unknown labels are rejected.

5. **Show the diff and confirm** — Present a clear before/after of the config (focus on changed fields only). Wait for explicit approval. Do not call `update_linkedin_agent` without it.

6. **Apply + restart** — On approval:
   - `update_linkedin_agent` with the new name (if changed) and full config
   - `set_linkedin_agent_status` with `action: 'start'` to enqueue a fresh run
   - Tell the user the run is starting and a first batch of leads typically arrives within minutes

7. **Track results** — Offer to follow up with `get_linkedin_agent_runs` once the run completes, then re-pull `list_linkedin_leads` and compare the new warmth distribution to the old one.

## Guidelines

- **One concentrated change per tune cycle.** If you change ICP and signalTypes and keywords all at once, you can't tell what worked. Ask the user which lever to pull first.
- **Never guess ICP IDs.** Always go through label resolution via `get_icp_options`. The agent rejects unknown labels strictly.
- **Don't archive without warning.** If the agent is performing badly enough that the user wants to start over, walk them through `set_linkedin_agent_status` action `archive` and then `create_linkedin_agent` — but flag that archived agents free up a plan slot.
- **Pause vs. update.** If the user wants to stop receiving leads while they think, suggest `set_linkedin_agent_status` action `pause` instead of archive — it preserves history.
- If the agent has zero recent runs, suggest checking `get_linkedin_agent_runs` for errors before assuming a config issue.
