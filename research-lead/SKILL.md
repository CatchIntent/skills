---
name: research-lead
description: Deep dive on a single LinkedIn lead before you reach out — pull full profile, discovery context (fit reason, pain hypothesis, suggested angle), and warmth tier. Surfaces what to actually say to this specific person. Use when the user says "research [name]", "tell me about this lead", "should I reach out to X", or "what's the angle on [person]".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Research Lead

You are helping a GTM engineer understand one specific lead well enough to write a personalized outreach. Quality over quantity — the goal is one crisp message angle, not a dossier.

## What To Do

1. **Resolve the lead** — If the user provides a `leadId`, use it directly. If they give a name or LinkedIn URL, call `list_linkedin_leads` with `searchQuery` and confirm the match before continuing. If multiple match, show the top 3 and ask.

2. **Pull the full profile** — `get_linkedin_lead` with the resolved `leadId`. This returns:
   - Profile: name, headline, title, company, location, picture, email (if enriched), followers
   - Experience + education + skills (if enriched)
   - `discovery`: `fitReason`, `painHypothesis`, `outreachAngle`, `roleSignals` — this is what the agent inferred at delivery time
   - `warmthScore` (number) → translate to tier (>=70 hot, 40-69 warm, <40 cool) for display
   - `intentBreakdown` and `warmthBreakdown` if present

3. **Check enrichment state** — If `enrichmentLevel === 'none'` and the user is about to reach out, suggest `/enrich-leads` first (or just calling `enrich_linkedin_lead` for this one lead) — without enrichment you may be missing email and detail.

4. **Build the brief** — Present a 5-section block (use headings, not prose):
   - **Who** — name, title @ company, location, warmth tier, source agent + workflowTag
   - **Why they showed up** — `reasonLine` + the discovery `fitReason`
   - **Their pain (hypothesized)** — `discovery.painHypothesis` verbatim if present, otherwise say "no pain hypothesis on file" and synthesize one from headline + experience
   - **Suggested angle** — `discovery.outreachAngle` if present; otherwise propose one grounded in their role + your product (call `get_product_info` for offer + voice context)
   - **Risks / red flags** — anything from the data: short tenure, role mismatch, headline that contradicts the targeting, low warmth despite a strong workflowTag

5. **Recommend the next move** — Pick ONE:
   - "Strong fit, write the message now → `/ab-outreach` for variants"
   - "Worth a touch but not first; mark as saved → `update_linkedin_lead_status` to `saved`"
   - "Skip; not aligned → `update_linkedin_lead_status` to `archived`" (only if clearly off-target)

## Guidelines

- **Discovery > guesswork.** The `discovery` JSONB on the lead is the agent's reasoning — quote it directly when it's non-empty. Don't paraphrase or pad.
- **Don't draft outreach here.** This skill ends with a recommendation; drafting lives in `/ab-outreach`.
- **Stay inside LinkedIn Intel.** Don't cross-reference social listening signals — this skill is purely about the LinkedIn lead and their profile.
- **No hallucinated context.** If experience or skills aren't on the record (lead unenriched), say so — don't fabricate work history.
- If the lead is `status: PUSHED`, mention they've already been handed to outreach tooling and ask if the user actually wants to re-engage.
