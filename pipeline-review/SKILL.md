---
name: pipeline-review
description: Pipeline health check — usage vs plan, agent activity, lead delivery + action rates, queue health. Use when the user says "how's my pipeline", "check my usage", "weekly review", "are we on track", or as a Monday-morning check-in.
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "2.0"
---

# Pipeline Review

You are giving a GTM operator a 60-second pipeline status. Numbers first, diagnosis second, action items last. This is the discovery side — agents, leads, and queue health. Outreach reporting will be added once the outreach module ships.

## What To Do

1. **Pull the headline numbers** — `workspace_usage` (period defaults to `month`). The shape is:

   - `workspace.products` `{ used, limit }`, `workspace.teamMembers` `{ used, limit, percentage }`
   - `subscription` (present only when there is an active subscription — if absent, the workspace is on a fresh trial or has no plan; say so and skip the usage table):
     - `subscription.monthly` `{ delivered, cap, periodStart, periodEnd }` — leads delivered this billing period
     - `subscription.daily` `{ delivered, cap }` — leads delivered today
     - `subscription.agents` `{ used, max }`
     - `subscription.stats` `{ delivered, toReview, hot, actedOn }` — `toReview` = leads still NEW, `hot` = warmth ≥ 70, `actedOn` = saved + pushed

   There is **no** pre-computed funnel in `workspace_usage`. Derive the action rate from `stats`: `actedOn / delivered`.

2. **Pull agent activity** — `agent_list`. Show active vs paused vs archived counts. For each active agent: name, signal types, `lastRunAt`. Flag any active agent whose `lastRunAt` is >48h old (or null).

3. **Pull queue health** — `lead_search` with `status: ['new']`, `pageSize: 1`, read `total`. If >50, suggest `/daily-triage`.

4. **Show the report** — one section per area, scannable:

   ### Usage (this period)
   | Metric | Used | Cap |
   | leads delivered (month) | `monthly.delivered` | `monthly.cap` |
   | leads delivered (today) | `daily.delivered` | `daily.cap` |
   | agents | `agents.used` | `agents.max` |
   | products | `products.used` | `products.limit` |

   ### Lead funnel (this period)
   - Delivered: `stats.delivered`
   - Still to review (NEW): `stats.toReview`
   - Hot (warmth ≥70): `stats.hot`
   - Acted on (saved): `stats.actedOn` → action rate `actedOn/delivered`

   ### Agents
   - N active, M paused, A archived
   - Stale: any active agent with `lastRunAt` >48h or null

   ### Queue
   - X new leads — link to `/daily-triage` if >50

5. **Diagnosis** — pick the lowest-performing area:
   - **Action rate <20%** → leads surfacing but not actioned. Queue overload or low-quality leads. Suggest `/daily-triage` or `/tune-agent`.
   - **Lots delivered, few hot** → agent targeting is loose. Suggest `/tune-agent` on the noisiest agent.
   - **Usage near cap** → flag plan limits; mention upgrade path.
   - **Stale agents** → suggest `/tune-agent` on the offenders.
   - **Empty queue + low delivery** → agents under-producing; check they're active and `lastRunAt` is recent.

6. **Monthly retro mode** — when the user asks for a "retro", "review last month", or when run monthly: add a lead-quality retro, not just current state.
   - For each active agent: `lead_search` by `agentId` with `status: ['saved']` vs `status: ['archived']` over the period. The saved:archived ratio is the agent's quality proxy — what the user kept vs threw away.
   - Surface the best agent (high saved ratio), the worst agent (high archived ratio), and which signal types dominate each.
   - Output: 3 concrete changes for next cycle (e.g. "kill `x_keyword_discussions` on agent X — 2 saved / 38 archived", "double down on `funding_signals` — 9 of 11 saved"). Link the worst agent to `/tune-agent`.

7. **Three actions** — end with exactly three concrete next steps, prioritized, each linking to a skill.

## Guidelines

- **Handle missing `subscription`.** No active subscription (fresh trial / no plan) means no usage caps yet — state that plainly instead of rendering an empty table.
- **Honest about thin data.** Action rate isn't meaningful with <10 delivered leads, or for an agent <14 days old. Say so explicitly.
- **No vague advice.** "Improve targeting" is useless; "Run /tune-agent on agent X — 38 of 40 leads archived, ICP too broad" is actionable.
- **Stay scannable.** Tables for numbers, bullets for actions. No paragraphs.
- **Per client.** For agencies, run this once per product (`product_list` → loop) so each client gets its own snapshot.
