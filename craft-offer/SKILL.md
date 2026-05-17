---
name: craft-offer
description: Sharpen the product's offer — headline, target pain point, unique mechanism, proof points. The offer is core brand context the AI uses to score and rank lead relevance. Use when the offer is vague/empty, when leads feel off-target, or when the user says "fix my offer", "what's my one-liner", "tighten my pitch".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Craft Offer

You are sharpening the structured offer on a product. The offer (pain point, mechanism, proof) is part of the product's brand context — the AI uses it to judge how relevant each surfaced lead is, so a sharp offer means tighter, better-scored leads. It also gives the team a clear, consistent pitch to use however they reach out.

## What To Do

1. **Get current state** — `product_get`. Show:
   - `brandInfo.offer.headline`
   - `brandInfo.offer.targetPainPoint`
   - `brandInfo.offer.uniqueMechanism`
   - `brandInfo.offer.proofPoints` (array)

   Plus context for sharpening: `description`, `competitors`, `replacesRoles`.

2. **Diagnose** — Each field, one line:
   - **Headline** — present + sharp, present + generic, or empty?
   - **Pain point** — names a specific problem the buyer has, or vague?
   - **Mechanism** — explains the *how* in 1 sentence?
   - **Proof points** — concrete claims with numbers / names, or marketing fluff?

3. **Walk through each field:**

   ### Target Pain Point
   The starting point. Ask: "What does your buyer hate doing today, that your product makes go away?" Push for specifics — not "outreach is hard" but "SDRs spend 4 hours/day on prospect research that gets ignored anyway."

   ### Unique Mechanism
   The how, in one sentence. Not "AI-powered" or "easy-to-use." Concrete: "We watch for the buying signals that precede a switch — job changes, funding, hiring — and surface the decision-maker with a scored, ready-to-send opener before competitors notice."

   ### Headline
   The one-liner. Pain + mechanism + outcome. Test against: "Could a competitor steal this verbatim?" If yes, sharpen.

   ### Proof Points
   3-5 concrete claims:
   - Metrics: "Customers see 4x reply rate vs cold lists"
   - Logos: "Used by Lemlist, Apollo, Clay"
   - Comparisons: "5x cheaper than ZoomInfo for the same data quality"

   No vague proof points like "trusted by hundreds." Numbers or names only.

4. **Show the proposed update** — Display the full offer block before writing.

5. **Confirm + write** — `product_update` with `brandInfo.offer`.

6. **Sanity-check** — Read the four fields back as a single short pitch. If a stranger couldn't tell what you do, who it's for, and why it's different in 10 seconds, it's not sharp enough — iterate. A sharper offer also tightens agent lead scoring, so it's worth getting right.

## Guidelines

- **Pain before mechanism before headline.** Build in that order. Most people want to start with the headline; that's why their headlines are generic.
- **One sentence, not a paragraph.** If a field needs a paragraph, you haven't sharpened it.
- **No marketing-speak.** "Best-in-class," "leading," "innovative" — strip these. Numbers, names, or specific claims only.
- **Don't conflate offer with description.** Description = what the product does. Offer = why someone should buy now.
- **Feeds lead scoring.** The offer is brand context the agent uses to judge fit — vague offer, looser leads. That's the main reason to keep it sharp.
