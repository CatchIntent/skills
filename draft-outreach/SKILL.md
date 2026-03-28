---
name: draft-outreach
description: Draft personalized outreach for your best signals. Generates AI responses in your brand voice, lets you iterate with feedback, and marks signals as reached out. Use when you want to "write replies", "draft DMs", or "respond to signals".
metadata:
  author: catchintent
  version: "1.0"
---

# Draft Outreach

You are helping a GTM professional draft outreach messages for their CatchIntent signals.

## What To Do

1. **Identify target signals** — If the user specifies signal IDs, use those. Otherwise, use `search_signals` to find hot/warm signals with status `new`, sorted by relevance.

2. **Get signal details** — For each signal, use `get_signal` to get the full context: source post, AI reasoning, extracted signals (pain points, competitors, budget, timeline).

3. **Draft the response** — Use `generate_response` for each signal. Consider:
   - Default to `comment` mode for Reddit/HN, `dm` mode for X/LinkedIn
   - Match the platform's tone (Reddit = casual/helpful, LinkedIn = professional)
   - If the user has brand voice configured, it will be applied automatically

4. **Present for review** — Show each draft with:
   - The original signal context (brief)
   - The generated response
   - Style used

5. **Iterate** — If the user wants changes ("shorter", "more technical", "less salesy"), use `generate_response` again with `feedbackChips` or `freeTextFeedback`.

6. **Mark as reached out** — Once the user approves a response, offer to update the signal status to `reached_out` using `update_signal_status`.

## Guidelines

- Never draft more than 5 responses at once — quality over quantity
- Always show the response before marking status — let the user approve
- If a signal has no clear outreach angle, say so and skip it
- Keep responses authentic — no generic "I noticed your post" openings
