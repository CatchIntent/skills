---
name: tune-agent
description: Improve an agent's targeting based on actual performance data — keyword rejection rates for keyword_listening agents, lead-quality patterns for LinkedIn agents. Use when the user says "tune my agent", "improve targeting", "agent X is delivering bad fits", or "fix this agent".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Tune Agent

You are diagnosing and fixing an underperforming agent. Always cite specific numbers — never vibes.

## What To Do

1. **Pick the agent** — `agent_list`. Ask which one if not specified. Show name + signal types so the user can confirm.

2. **Get full state** — `agent_get` for the agent. Note: signals[], targeting (keywords, competitors, ICP, influencers), status, lastRunAt.

3. **Pull product context** — `product_get` to understand the brand, ICP, voice. Tuning has to align with the offer.

4. **Diagnose by signal type:**

   **For keyword-discussion agents (`keyword_discussions` LinkedIn or `x_keyword_discussions`):**
   - Recent leads: `lead_search` with `agentId`, `status: ['archived']`, page size 50. Look at the surfacing posts — are they consistently off-topic? What pattern?
   - Common fixes:
     - **Keyword too broad** — `agent_update` with narrower `targeting.keywords` (replace `"project management"` with `"project management for agencies"`)
     - **Mismatch between platforms** — if LI keyword discussions deliver well but X is noisy (or vice versa), drop the underperforming signal type from `signals[]` and let the other run alone

   **For LinkedIn ICP / engagement agents (`icp_search`, `competitor_engagers`, `funding_signals`, `hiring_signals`, `job_change_signals`, `influencer_engagers`):**
   - Recent leads: `lead_search` with `agentId`, `status: ['archived']`, page size 30
   - Look for patterns: wrong industry? wrong seniority? wrong geography? company-stage mismatch?
   - Common fixes:
     - **Wrong industry** — `workspace_icp_options` then `agent_update` with refined `targeting.icp.industries` or `excludeIndustries`
     - **Wrong seniority** — same pattern with `seniorityLevels` / `excludeSeniorityLevels`
     - **Wrong company size** — adjust `companySizes`
     - **Bad signals** — drop a strategy from `signals[]` if it consistently produces bad leads (e.g., remove `funding_signals` if funded-company leads aren't fitting)

5. **Show diagnosis with citations** — Before suggesting changes, show the data:
   - "12 of last 30 archived leads were in 'Staffing and Recruiting' industry — you don't sell to staffing agencies."
   - "Keyword 'CRM' has 847 matches, 92% rejection rate — too generic."

6. **Propose specific changes** — Show the exact `agent_update` payload you'd send. Wait for approval. Then call.

7. **Confirm + suggest follow-up** — After the update, suggest re-checking lead quality in 3-5 days. If the agent is currently active, confirm whether to keep it active or pause until the user is sure (`agent_set_status` with `pause`).

## Guidelines

- **Numbers before opinions.** Quote rejection rates, archived-count patterns, ICP match deltas.
- **One change at a time.** Don't fix industry + seniority + keywords in one shot. Pick the highest-impact issue first; let the user re-run later for the next one.
- **Strict ICP labels.** ICP fields only accept labels from `workspace_icp_options`. Always check before suggesting a label that wasn't already on file.
- **Don't tune the product instead.** If the agent is delivering well but the user is unhappy, the issue may be the offer — point at `/craft-offer`.
- **Don't archive the agent without asking.** If you suspect the whole strategy is wrong, suggest pausing first to investigate.
