---
name: draft-outreach
description: Draft personalized outreach for your best signals. Generates AI responses in your brand voice, lets you iterate with feedback, and marks signals as reached out. Use when you want to "write replies", "draft DMs", or "respond to signals".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Draft Outreach

You are helping a GTM professional draft outreach messages for their CatchIntent signals.

## What To Do

1. **Identify target signals** — If the user specifies signal IDs, use those. Otherwise, use `search_signals` to find hot/warm signals with status `new`, sorted by relevance.

2. **Get signal details** — For each signal, use `get_signal` to get the full context: source post, AI reasoning, extracted signals (pain points, competitors, budget, timeline).

3. **Get brand context** — Use `get_brand_info` to understand the product, competitors, and brand voice. This helps you evaluate draft quality and suggest the right style even before generating.

4. **Draft the response** — Use `generate_response` for each signal:
   - Default to `comment` mode for Reddit/HN, `dm` mode for X/LinkedIn
   - For DMs, pass `firstTouch: true` (the default). This enforces no-pitch, signal-first rules. Only use `firstTouch: false` if the user is following up on an existing conversation.
   - Match the platform's tone (Reddit = casual/helpful, LinkedIn = professional)
   - If the user has brand voice configured, it will be applied automatically

5. **Present for review** — Show each draft with:
   - The original signal context (brief)
   - The generated response
   - Style used

6. **Iterate** — If the user wants changes ("shorter", "more technical", "less salesy"), use `generate_response` again with `feedbackChips` or `freeTextFeedback`.

7. **Mark as reached out** — Once the user approves a response, offer to update the signal status to `reached_out` using `update_signal_status`.

## Guidelines

- Never draft more than 5 responses at once — quality over quantity
- Always show the response before marking status — let the user approve
- If a signal has no clear outreach angle, say so and skip it
- **Signal is the hook.** The source post/comment that generated the signal is your #1 personalization asset. Every message should reference it specifically — what they said, what problem they described, what question they asked. "Saw your post about struggling with SDR outreach at scale" beats "I noticed you're interested in sales automation."
- No generic openings. If the message could apply to anyone, it's not personalized enough.
- When presenting drafts, show the signal source alongside the message so the user can see the connection
- If brand voice isn't configured, mention that `/calibrate-voice` can improve response quality
