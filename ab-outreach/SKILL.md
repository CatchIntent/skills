---
name: ab-outreach
description: Draft 2-3 outreach variants for a single LinkedIn lead — different pain angles, different touch types — and let the user pick the winner before sending. Surfaces what message actually works for this specific person rather than blasting one templated draft. Use when the user says "draft DMs for [name]", "write outreach for this lead", "give me variants", "test different angles", or "A/B this message".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# A/B Outreach

You are helping a GTM engineer compose the actual message they'll send to a LinkedIn lead. The goal is **a small set of distinct angles**, not three near-identical drafts. The user picks one, and you mark the lead so they remember it's queued.

## What To Do

1. **Resolve the lead** — `leadId` if given; otherwise `list_linkedin_leads` `searchQuery` to look up by name and confirm the match.

2. **Load context** — Call `get_linkedin_lead` with `productId` + `leadId`. Check:
   - `discovery.painHypothesis` — the agent's primary read
   - `discovery.outreachAngle` — suggested angle
   - `discovery.fitReason` — why they matched
   - `enrichmentLevel` — if `none`, flag that messages will be thinner; offer `/enrich-leads` first
   The product's brand voice, offer, and tone come from `get_product_info` if needed (the drafter pulls them automatically, but reading them helps you explain *why* a variant sounds the way it does).

3. **Decide which variants to draft** — Ask the user one quick question OR pick sensible defaults:
   - **Touch purpose**: usually `connect_note` (first contact, ≤260 chars) or `first_dm` (after they accept). For follow-ups on existing threads, `proof`, `follow_up`, `breakup`, or `loom_ask`.
   - **Pain angles**: pull 2-3 distinct angles. Default set:
     - Variant A: the agent's `painHypothesis` verbatim (no override)
     - Variant B: a sharper angle pulled from the lead's role/title (use `painHypothesisOverride`)
     - Variant C: a contrarian angle — the *opposite* hypothesis (use `painHypothesisOverride`)
   The user can override before you draft.

4. **Draft each variant in parallel** — Call `draft_linkedin_outreach` 2-3 times, varying `purpose` and `painHypothesisOverride`. Each call returns body + rationale + (optional) variants.

5. **Present side-by-side** — Show as a table or numbered blocks:
   ```
   Variant A — pain: [hypothesis]
   "[body]"
   Why: [rationale]

   Variant B — pain: [override]
   "[body]"
   Why: [rationale]
   ```
   Keep the rendering tight — bodies + one-line rationales, no padding.

6. **Get the pick** — Ask "which variant, or want to iterate?" Options:
   - **Pick one** → Show the chosen body cleanly so the user can copy-paste; offer to mark the lead as `saved` (if not already) via `update_linkedin_lead_status`. Do **not** auto-set to `pushed` — that status is reserved for actual handoff to an outreach tool, not "I drafted it."
   - **Iterate** → Take feedback ("shorter", "less salesy", "drop the question"), redraft that one variant by calling `draft_linkedin_outreach` again with adjusted `painHypothesisOverride` or different `purpose`.
   - **Reject all** → Suggest enriching the lead first, or running `/research-lead` to surface a sharper angle, then come back.

## Guidelines

- **2-3 variants max.** More than that is decision paralysis. The point is to make a fast pick, not run a science experiment.
- **Distinct angles, not synonym swaps.** If two variants say the same thing in different words, kill one and replace it with a genuinely different pain hypothesis.
- **Respect channel limits.** Connect notes are ≤260 chars; first DMs ≤400. The drafter enforces this, but call it out in the presentation so the user can see why a variant feels short.
- **Never auto-send.** The skill ends with a copy-pasteable body. Sending happens in the user's outreach tool (Lemlist, Instantly, manual DM, extension).
- **Don't mark `pushed` from this skill.** `pushed` means handoff to an outreach tool actually happened. Use `saved` to indicate "drafted, ready to send."
- **Stay inside LinkedIn Intel.** No social listening cross-ref, no signal pulls. The lead's own `discovery` block is the input.
- If the lead is already `pushed` or `archived`, ask why the user is drafting outreach — sequence may already be running.
