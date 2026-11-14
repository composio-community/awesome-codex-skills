---
name: creative-psychology-hooks
description: Generate and grade ad hooks, angles and video openers with a creative psychology framework (TEEP chain, valence/intensity, hook mechanism, micro-moment). Use when the user asks for hook ideas, ad angles or openers for Meta, TikTok or YouTube ads, or wants existing hooks critiqued on why they would or would not stop the scroll. Works with no data source; grounding hooks in the user's own ad results or competitors' live ads needs the GoMarble MCP connector.
---

# Creative Psychology Hooks

Generates and grades ad hooks and angles with a structured psychological framework instead of vibes-based brainstorming. Works standalone for cold ideation, or as the "why would this work" lens on top of creative patterns pulled from the user's own ad account or from competitors' ads.

Adapted from the `creative-psychology-hooks` skill in [gomarble-ai/marketing-agent](https://github.com/gomarble-ai/marketing-agent) (MIT, see `LICENSE.txt`).

## When to use this skill

- "Give me hook ideas for [product]"
- "Why would this angle work, or not work, psychologically?"
- Grading a batch of hooks before they go into a creator brief
- Adding a psychological-mechanism layer to competitor or own-account creative patterns

## Data sources

- **No connector (default):** cold ideation and grading from the product, audience and any ad copy, scripts or results the user pastes in.
- **GoMarble MCP connector (needed for live ad data):** to build hooks from the user's own top-spending ads or from competitors' running ads, this skill needs the GoMarble MCP connector, a remote MCP server at `https://apps.gomarble.ai/mcp-api/mcp` with OAuth sign-in and a GoMarble account. One way to add it to Codex is the GoMarble plugin:

  ```bash
  codex plugin marketplace add https://github.com/gomarble-ai/marketing-agent.git
  codex plugin add gomarble@gomarble
  codex mcp login gomarble
  ```

  Without the connector, never invent account metrics or competitor ads. Ask the user to paste them, or run in cold-ideation mode and say so.

## Step 1: Gather product and audience context

Before generating anything, confirm (infer from the conversation where possible, ask only what's missing):
- Product or offer and its core benefit
- Target audience: who specifically, and what state they're in when they see the ad (scrolling passively vs actively searching)
- Primary objection or hesitation this audience has toward the category
- Tone constraints, if any (brand voice, platform norms)

## Step 2: Apply the framework

### TEEP (Thought, Emotion, Expression, Physiology)
For each candidate hook, trace the chain it's meant to trigger in the viewer:
- **Thought**: what cognitive frame does the opening line or visual plant? ("this is about me," "this is a problem I have," "this is surprising")
- **Emotion**: what feeling follows from that thought?
- **Expression**: what does that emotion look and sound like: the tone, pacing and visual choice that should carry it
- **Physiology**: what physical reaction is being aimed for: lean in, laugh, wince, nod. This is the tell for whether a hook will actually stop a scroll.

A hook that skips straight to Expression without a real Thought to Emotion chain behind it reads as hollow or clickbait. Flag these.

### Valence / Intensity Mapping
Plot each hook on two axes:
- **Valence**: positive (aspiration, relief, humor) vs negative (fear, frustration, FOMO)
- **Intensity**: low (mild curiosity) vs high (urgent, provocative)

High-intensity negative hooks (fear, frustration) may attract attention but can create brand fatigue or backlash risk if overused. Low-intensity positive hooks may fit the brand better but should not be assumed to stop the scroll. Treat these as test hypotheses, account for the audience's tolerance, and recommend a spread across the matrix rather than five hooks in one quadrant.

### Hook mechanism (Baader Hook Framework)
Classify each hook by mechanism, not just topic:
- **Pattern interrupt**: visually or verbally breaks the expected feed rhythm
- **Open loop**: poses a question or tension the viewer needs resolved
- **Direct claim**: bold, specific, falsifiable-sounding statement
- **Identity call-out**: names the exact audience ("if you have curly hair...")
- **Social proof lead**: opens with someone else's result or reaction, not the brand's claim

Every hook in a batch should be tagged with its mechanism. A batch that's 80% one mechanism isn't actually a diverse test. Flag that too.

### Micro-Moment Mapping
Identify the specific real-world moment the hook is trying to interrupt or attach to (e.g. "just noticed hair thinning in a photo," "scrolling in bed frustrated with current routine"). Concrete moments produce more specific, testable creative hypotheses than generic "are you tired of X" openers; do not claim they will outperform without account evidence.

## Step 3: Generate

Produce hooks in batches of 8 to 10 by default (adjust if the user asks for fewer or more). Spread deliberately across:
- Valence/intensity quadrants
- Hook mechanisms
- At least 2 distinct micro-moments

## Step 4: Grade (when reviewing existing hooks)

For each hook, output: TEEP chain (does it hold together?), valence/intensity position, mechanism, micro-moment specificity, and a one-line verdict on why it would or wouldn't stop a scroll for this audience.

## Output format

```
### Hook Set: [Product/Audience]

Context: [product, audience, objection, tone]
Data used: [none (cold ideation) | pasted by user | GoMarble MCP: own account / competitor ads]

| # | Hook | Mechanism | Valence/Intensity | Micro-moment | Why it may work |
|---|---|---|---|---|---|

Coverage check: [mechanisms used, quadrants covered, any gaps]
```

## Guardrails

- Don't force every hook into every framework layer if it doesn't fit. A strong hook that's hard to TEEP-trace cleanly is still worth including; note the ambiguity rather than distorting the analysis.
- Avoid defaulting to fear or negative-valence hooks purely because they're easier to generate. Deliberately include positive-valence options.
- Hooks are hypotheses to test, not predicted winners. Never state expected CTR, hook rate or ROAS without account evidence.
- This skill only reads data and writes text. It never edits campaigns or ads.
- It produces angles and hook lines, not full scripts. For shoot-ready creator briefs, hand off to a brief-writing skill (the GoMarble plugin ships `ad-brief-generator`).
