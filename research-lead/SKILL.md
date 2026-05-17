---
name: research-lead
description: Deep research on a single LinkedIn person-lead — public web sources first, paid enrichment only if needed. Builds a sharp 60-second profile and the angle to lead with. Use when the user says "research X", "what do I need to know about this lead", or before acting on a high-stakes lead.
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Research Lead

You are building a 60-second briefing on a LinkedIn lead before the user reaches out. Person-shaped only — X leads are pseudonymous, no profile to research.

## What To Do

1. **Get the lead** — `lead_get` with `leadId`. Read everything: name, title, company, location, `discovery.painHypothesis`, `discovery.fitReason`, `discovery.outreachAngle`, role signals, warmth tier, `sourcePlatform`.

2. **Get product context** — `product_get`. Note offer, ICP, competitors. Research is targeted: you're not building a profile, you're finding the wedge.

3. **Try free sources first.** In this order, only as needed:
   - **LinkedIn URL** — `WebFetch` the public profile. Pull recent posts, role tenure, prior companies.
   - **Company website / about page** — `WebFetch` the company domain. Size, focus, recent moves.
   - **X / GitHub / podcast / blog** — `WebSearch` `"<name>" site:twitter.com OR site:github.com`, podcast appearances, blog posts.
   - **Recent funding / news** — `WebSearch` `"<company>" funding 2025` for a recent angle.

4. **Decide on paid enrichment** — Only if free sources didn't give you enough to write outreach AND the user explicitly approves:
   - `lead_enrich` with `leadId` (≈$0.002, idempotent — won't re-charge if `fullEnrichmentAt` is set)
   - Show what the user gets vs what they'd pay
   - If `fullEnrichmentAt` is already set, do NOT call again — read the existing data instead

5. **Build the briefing**:

   ### One-liner
   "{Title} at {Company} — {one-sentence angle}"

   ### Why they're a fit
   - {ICP match details}
   - {Pain hypothesis from discovery}
   - {Recent signal that surfaced them}

   ### Recent context (free sources)
   - {LinkedIn post / X take / podcast quote / company news — anything that gives an opener}

   ### Angle to lead with
   The single strongest hook from the briefing — the one piece of evidence most likely to earn a reply when the user reaches out.

6. **Hand off** — Present the briefing and the angle. The user takes it into whatever channel they reach out on.

## Guidelines

- **Free before paid.** Apify enrichment is one-shot and charged whether or not data is found. Try every public source first.
- **One angle, not a profile.** The user doesn't need a Wikipedia entry — they need the one thing that earns a reply.
- **Cite sources.** "Posted on LinkedIn 2 days ago about scaling SDR team" is a hook. "Seems interested in sales" is not.
- **Don't fabricate.** If you can't find anything noteworthy, say so. Better to use the stored `painHypothesis` than to invent a fake hook.
- **Idempotent.** Don't re-call `lead_enrich` if `fullEnrichmentAt` is already set.
