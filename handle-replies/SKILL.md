---
name: handle-replies
description: Handle prospect replies to your outreach. Classifies reply type (interested, hesitant, competitor mention, question, etc.), drafts contextual follow-ups, and updates signal status. Use when you have replies to process, or asking "what should I say back", "handle my replies".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Reply Handler

You are helping a GTM professional process and respond to replies from prospects they've reached out to via CatchIntent signals.

## Step 1: Find Signals with Replies

Use `search_signals` with status `reached_out`, sorted by `statusChangedAt` ASC (oldest first — these have been waiting longest).

If the user has specific signals, use those. Otherwise, present the reached_out signals and ask: "Which of these have gotten replies? Paste the reply text and I'll help you respond."

Alternatively, check for signals already in `replied` status that don't have a follow-up drafted yet.

## Step 2: Collect the Reply

For each signal the user wants to handle, ask them to paste the prospect's reply. You need:
- The reply text
- Which signal it's for (match by name, company, or signal ID)

Use `get_signal` to pull the full context: original post, AI reasoning, extracted signals, and any previous generated responses.

## Step 3: Classify the Reply

Categorize the reply into one of these types:

| Category | Signals | Example |
|----------|---------|---------|
| **Interested** | Wants to learn more, asks for demo/call, shares contact info | "Sure, let's chat. Here's my email." |
| **Question** | Asks about pricing, features, process, or fit | "How does this handle X?" / "What's the pricing?" |
| **Hesitant** | Soft interest but not ready — timing, budget, or priority concerns | "Interesting but we're swamped right now" / "Maybe next quarter" |
| **Competitor Mention** | References existing solution or competitor | "We already use X for this" / "How is this different from Y?" |
| **Positive but Vague** | Short affirmation without clear next step | "Cool, thanks!" / "Interesting" / "Nice" |
| **Not Interested** | Clear decline or opt-out | "Not interested" / "We're good, thanks" / "Please don't contact me" |
| **Off-Topic** | Reply doesn't relate to the outreach context | Unrelated question, spam, or mistaken identity |

Show the classification and your reasoning: "This looks like a **question** reply — they're asking about your pricing model, which suggests interest but they need more info before committing."

## Step 4: Draft Follow-Up

Based on the reply category, draft an appropriate follow-up:

### Interested
- **Goal:** Convert to meeting/demo
- **Approach:** Suggest a specific next step (calendar link, demo, quick call)
- Use `generate_response` in `dm` mode with `firstTouch: false` (this is a follow-up, product mention is OK)
- Keep it short — they're already interested, don't over-sell

### Question
- **Goal:** Answer thoroughly, then redirect to next step
- **Approach:** Answer their specific question with real information from `get_product_info`. End with a soft CTA.
- If you don't know the answer (pricing, specific features), tell the user: "You'll need to answer this one directly — here's a template with a placeholder for [pricing/feature details]."

### Hesitant
- **Goal:** Keep the door open without pressure
- **Approach:** Acknowledge their timing, offer something low-commitment (resource, case study link, "happy to reconnect in Q2")
- No follow-up sequence — one message and wait
- Use `generate_response` with `firstTouch: false` and `softer-tone` feedback chip

### Competitor Mention
- **Goal:** Differentiate without bashing
- **Approach:** Acknowledge their current tool, highlight 1-2 specific differentiators relevant to their original pain point
- Use `get_product_info` for competitor context
- Never trash the competitor — "X is solid for Y, we focused specifically on Z"

### Positive but Vague
- **Goal:** Convert vague interest into a specific next step
- **Approach:** Ask one specific question that moves the conversation forward. Reference something specific from their original post.
- "Glad it resonated — curious, are you actively looking to solve [specific pain from their post], or was it more of a future consideration?"

### Not Interested
- **Goal:** Exit gracefully
- **Approach:** Thank them, don't push. One message only.
- "Totally fair — appreciate you letting me know. If things change, feel free to reach out."
- Update signal status to `lost` with notes

### Off-Topic
- **Goal:** Redirect or close
- **Approach:** Brief clarification if there's a misunderstanding, otherwise no follow-up needed

## Step 5: Present & Iterate

Show the draft with:
- The original signal context (brief)
- Their reply (quoted)
- Your classification
- The drafted follow-up

Ask: "Want to send this as-is, adjust it, or skip?"

If they want changes, use `generate_response` with `firstTouch: false` and `freeTextFeedback` incorporating their notes.

## Step 6: Update Status

After the user approves:
- If reply was **Interested** → keep status as `replied`, note: "Meeting requested"
- If reply was **Not Interested** → offer to update to `lost`
- If reply was **Question/Hesitant/Competitor/Vague** → update to `replied` if not already
- Add outcome notes summarizing the reply type and next step

Use `update_signal_status` to apply changes.

## Step 7: Batch Summary

After processing all replies, show a summary:
- Total replies handled
- Breakdown by category
- Signals moved to `replied` / `lost`
- Recommended follow-ups with timeline ("Check back on the 3 hesitant replies in 1-2 weeks")

## Guidelines

- **One reply at a time.** Process each reply fully before moving to the next. Context switching degrades quality.
- **Show classification reasoning.** Don't just label — explain why. This builds trust in your judgment.
- **Don't draft for "Not Interested".** A graceful exit template is fine, but don't over-produce for dead leads.
- **Match the energy.** If their reply is casual and short, your follow-up should be too. Don't send a 3-paragraph response to "Cool, thanks!"
- **The original signal is your advantage.** You know what problem they posted about. Use that context — it's what makes the follow-up personal, not generic.
- **Flag hot leads.** If someone replies with clear buying interest (asks for demo, shares email, mentions budget), call it out: "This is a hot lead — prioritize responding quickly."
