---
name: craft-offer
description: Craft or refine your product offer — the value proposition, pain point, unique mechanism, and proof points that power all AI-generated outreach messages. Use when setting up your offer for the first time, when outreach messages feel generic, or when you want to sharpen your positioning.
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Craft Offer

You are helping a GTM professional build a compelling product offer framework that will power all their AI-generated outreach on LinkedIn, Reddit, and other platforms.

## What To Do

1. **Get current state** — Use `get_brand_info` to check:
   - Does the workspace already have an offer configured? (check `offer` field)
   - What's the product name and description?
   - Who are the competitors?
   - What's the ICP (target roles, industries)?

2. **Assess the situation**:
   - If offer exists: show it and ask "Want to refine this or start fresh?"
   - If no offer: "Let's build your offer from scratch. I'll ask a few questions."

3. **Gather inputs** — Ask these questions one at a time (don't overwhelm):
   - "What specific problem does your product solve? Describe it from the customer's perspective — what are they experiencing before they find you?"
   - "What makes your approach different from [competitors from brand info]? Not features — HOW you solve it differently."
   - "What proof do you have? Metrics, customer results, testimonials, awards — anything concrete."
   - "What's your ideal next step when someone's interested? (e.g., free trial, demo call, sandbox access)"

4. **Draft the offer** — From their answers + brand context, create:
   - **Headline**: Benefit-focused, under 15 words. Not a tagline — a clear value statement.
   - **Target Pain Point**: Written from the buyer's perspective. What they're experiencing, not what they should want.
   - **Unique Mechanism**: The "how" — what makes this approach different. Reference competitors if relevant.
   - **Proof Points**: 3-5 concrete, verifiable facts. Derive from their answers or the product description if they don't have hard metrics yet.
   - **Call to Action**: Clear, specific, low-friction.
   - **Risk Reversal** (optional): What removes risk — guarantee, free tier, no commitment.
   - **Pricing Context** (optional): Brief framing if it helps positioning.

5. **Present for review** — Show the complete offer framework in a clean table. Ask: "How does this feel? What would you change?"

6. **Iterate** — Common adjustments:
   - "Too salesy" → soften the headline, make pain point more empathetic
   - "Too vague" → add specifics from their answers, use numbers
   - "Doesn't sound like us" → check `/calibrate-voice` for tone alignment
   - "Missing our key differentiator" → refine the unique mechanism

7. **Save** — Once approved, use `update_brand_info` to save the offer. Show what was saved.

8. **Test it** — Find a recent hot signal with `search_signals`, then use `generate_response` to draft a message. Show how the offer context improves the outreach: "Here's what a response looks like WITH your offer vs a generic one."

## Guidelines

- **Concrete > clever.** "95% noise reduction" beats "best-in-class filtering" every time.
- **Buyer's words, not seller's.** The pain point should use language the buyer would use, not marketing copy.
- **Proof points must be real.** If they can't provide metrics, derive plausible ones from the product description — but flag them as "suggested, verify before using."
- **One at a time.** Don't ask all 4 questions at once. This is a conversation, not a form.
- **Show the impact.** After saving, generate a test response so they see how the offer improves their outreach quality.
- **Cross-reference.** If ICP isn't configured, suggest `/calibrate-voice` after offer is set. If they want to test on real signals, suggest `/draft-outreach`.
