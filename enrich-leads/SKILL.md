---
name: enrich-leads
description: Enrich LinkedIn leads with deeper context — but always try free public web sources first (LinkedIn, company site, X, GitHub, podcast/blog mentions) before spending Apify credits via the paid enrich tool. Defers paid enrichment when free sources already give you enough to write outreach. Use when the user says "enrich [name]", "get me more on this lead", "find emails", or "fill out the profile".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Enrich Leads

You are helping a GTM engineer enrich one or more LinkedIn leads. The paid `enrich_linkedin_lead` tool costs money (Apify ~$0.002 per call) and is one-shot — once spent, it's spent. **Always try free public sources first.** Only fall back to the paid enrich when free sources don't give enough to write a good outreach.

## What To Do

1. **Resolve the targets** — If the user names a single lead, look it up via `list_linkedin_leads` `searchQuery`. For batch enrichment, ask which filter to apply: a specific agent, hot+warm tier, or saved status. Use `list_linkedin_leads` with those filters. Confirm the count before processing.

2. **For each lead, decide what's missing** — Call `get_linkedin_lead` and check what's already there:
   - **Has email + headline + experience + skills + about** → already enriched, skip entirely
   - **Has name + company + title + linkedinUrl but missing email/about/experience** → try web research first (step 3)
   - **`fullEnrichmentAt` is set but enrichment was empty** → don't retry; the paid tool already failed

3. **Web-research path (default — free)** — Use `WebFetch` and `WebSearch` to gather public info before spending credits. Try in this order:
   - **The lead's LinkedIn URL itself** via WebFetch (often returns the public bio + recent post snippets)
   - **Their company website's About / Team page** (`{company}.com/about`, `/team`, `/leadership`)
   - **A targeted Google search**: `"{full name}" "{company}" site:linkedin.com OR site:x.com OR site:twitter.com`
   - **Podcast / blog appearances**: `"{full name}" "{company}" podcast OR interview OR talk`
   - **GitHub / Crunchbase / personal site** if their headline suggests technical or founder context
   Aggregate findings into a brief: about, recent role moves, public projects, talks, distinctive interests.

4. **Decide if enrichment is needed** — After the web pass, ask yourself (and tell the user):
   - Do you have enough to write a personalized first-touch DM? **Yes → stop, do not call `enrich_linkedin_lead`.** Free path won.
   - Did web research yield nothing useful (private profile, generic site, no public footprint)? **Then escalate.**
   - Do you specifically need their email for an outbound sequence? **Then escalate** (only the paid tool returns email).

5. **Confirm before spending** — If escalating, tell the user explicitly: "Web sources didn't give us enough; running paid enrichment for [name] (≈$0.002, one-shot, charged whether or not data is found)." Wait for approval. For batch escalation, show the exact list and count, then approve once.

6. **Run paid enrichment** — `enrich_linkedin_lead` with `productId` + `leadId`. The tool is idempotent — if it's already been run, it returns the existing record without re-spending. Surface what came back: email (if any), enrichment level, total cost across providers from `enrichmentCostsUsd`.

7. **Summarize the haul** — For each lead, present a final brief combining:
   - Free-research findings (cite sources with URLs)
   - Paid enrichment results (if applicable)
   - Suggested outreach angle grounded in the combined context
   - Pointer: "Run `/ab-outreach` next to draft variants for this lead"

## Guidelines

- **Default to free.** Most leads have enough public footprint that web research gives you a personalization hook. Paid enrichment exists for the long tail and email capture, not the default path.
- **Never paid-enrich silently in a loop.** Even in batch mode, get one explicit approval per batch with the count and dollar estimate.
- **Don't over-research.** Two or three web fetches is usually enough. If you're four queries in and still finding nothing, the lead is private — escalate to paid or skip.
- **Cite sources.** When using web findings in a brief, link the URL. Unsourced claims about a real person are a liability.
- **Idempotency is your friend.** `enrich_linkedin_lead` is one-shot — `fullEnrichmentAt` marks "we already tried", not "we succeeded". Don't propose retrying a lead that has the timestamp set; the data isn't going to magically appear.
- **No fabrication.** If the web returns nothing and the user declines paid enrich, say "not enough public info to personalize" — don't pad with generic role descriptions.
- If you find them on X, GitHub, or a podcast, that's gold for personalization — flag it explicitly: "Their last X post was about [topic] — strong opener."
