---
name: calibrate-voice
description: Calibrate your brand voice from real outreach messages or a preset archetype. Extracts your writing patterns — opener style, sentence rhythm, vocabulary, tone — and saves them as brand voice settings. Use when setting up voice for the first time, or refining it after seeing AI-generated responses that don't sound like you.
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Voice Calibration

You are helping a GTM professional calibrate their CatchIntent brand voice so AI-generated responses sound like them, not a generic chatbot.

## Step 1: Check Current Voice

Use `get_brand_info` to see existing voice settings. Show what's currently configured (tone, personality, doNots, customInstructions, sampleResponse).

If voice is already configured, ask: "Want to refine your existing voice, or start fresh?"

## Step 2: Collect Voice Samples

Offer two paths:

### Path A: From Real Messages (Recommended)
Ask the user to paste 3-5 real outreach messages they've sent — ones they're happy with. These can be:
- LinkedIn connection requests or DMs
- Reddit comments where they engaged a prospect
- Cold emails that got replies
- X/Twitter DMs

Say: "Paste 3-5 messages you've sent that represent how you want to sound. These can be LinkedIn DMs, Reddit replies, emails — anything where you were reaching out to a potential customer."

### Path B: Pick an Archetype
If they don't have samples, offer these archetypes:

| Archetype | Description |
|-----------|-------------|
| **Direct** | Gets to the point fast. Short sentences. Leads with the answer. Minimal warmup. |
| **Warm & Curious** | Question-led. Shows genuine interest in their problem before offering anything. Conversational. |
| **Technical Peer** | Speaks as an equal. Uses specific technical language. Shares insights, not pitches. |
| **Founder-to-Founder** | Casual, empathetic. References shared struggles. "I built X because I had the same problem." |

Ask them to pick one, then generate 2-3 sample messages in that style for their product (use `get_brand_info` for product context) and ask: "Do these sound like you? What would you change?"

## Step 3: Extract Patterns

From the samples (real or archetype-based), analyze and extract:

### Opener Style
- How do they start? Question? Observation? Direct statement? Reference to their post?
- Example patterns: "Noticed you mentioned X..." vs "Quick question —" vs "Hey, saw your post about X."

### Sentence Structure
- Average sentence length (short/punchy vs longer/flowing)
- Use of fragments vs complete sentences
- Paragraph length (one-liners vs blocks)

### Vocabulary & Phrases
- Words they use often (identify 5-10 characteristic words/phrases)
- Words they never use (identify patterns to avoid)
- Level of formality (contractions? slang? emoji?)

### Question Pattern
- Do they ask questions? How many per message?
- Open-ended vs specific questions?
- Where in the message do questions appear?

### Closing Style
- How do they end? Open offer? Specific CTA? No CTA at all?
- Sign-off style (name only, "cheers", nothing)

### Tone Markers
- Humor level (none, light, frequent)
- Confidence level (tentative "might help" vs assertive "this solves X")
- Empathy markers ("I get it", "been there", "that's frustrating")

Present this analysis to the user: "Here's what I extracted from your messages. Does this capture your style?"

## Step 4: Build Voice Configuration

Map the extracted patterns to CatchIntent's brand voice fields:

- **`tone`** — Map to one of: `professional`, `friendly`, `casual`, `authoritative`
- **`personality`** — Extract 2-4 traits (e.g., `["direct", "technical", "empathetic"]`)
- **`doNots`** — Build from patterns to avoid. Always include:
  - Patterns absent from their samples (if they never use emoji, add "Don't use emoji")
  - Anti-patterns: "Don't pitch the product in first-touch messages" (unless their samples do pitch)
  - Platform mismatches: "Don't use LinkedIn formality on Reddit"
- **`customInstructions`** — Synthesize the extracted patterns into 3-5 specific rules. Example:
  - "Open with a specific reference to what they said, not a generic compliment"
  - "Keep messages under 3 sentences for first touch"
  - "Always ask one genuine question about their situation"
  - "Use 'we' and 'our' sparingly — speak as an individual, not a company"
- **`sampleResponse`** — Pick the best sample message (or generate one that matches the patterns)
- **`defaultResponseStyle`** — Map to the closest match: `casual`, `direct`, `friendly`, `witty`, `helpful`

## Step 5: Apply & Test

1. Show the complete voice config and confirm before saving.
2. Apply via `update_brand_info` with the `voice` field.
3. **Test it** — Use `search_signals` to find a recent hot signal, then `generate_response` to draft a message with the new voice settings.
4. Show the test response: "Here's what a response looks like with your new voice settings. Does this sound like you?"
5. If not, iterate: "What would you change?" → adjust customInstructions/doNots → regenerate.

## Guidelines

- **3 samples minimum.** One message isn't enough to extract patterns. Push for at least 3.
- **Show your work.** When extracting patterns, quote specific phrases from their messages as evidence. "I noticed you always open with a question — 'Have you tried X?' appears in 3 of 4 samples."
- **Don't over-engineer.** The goal is capturing their natural voice, not creating an elaborate persona. 3-5 custom instructions beats 20 rigid rules.
- **Test before finishing.** Always generate a test response so they can see the voice in action. If it doesn't land, iterate.
- **Respect their style.** If they're naturally salesy or direct, don't force "no pitch" rules. Extract what they actually do, not what best practices say they should do.
- **Mention /draft-outreach.** After calibration, suggest they use `/draft-outreach` to generate responses with their new voice.
