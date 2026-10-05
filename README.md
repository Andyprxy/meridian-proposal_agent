# AI Engineer Challenge — pack contents

Everything you need for the take-home. **Start with `BRIEF.md`.**

All data in this pack is **fictional** — the firm "Meridian Wealth", its people
and its model portfolios are invented. There is no real client or company data.

## What's in here (candidate-facing)

| File | What it is |
|------|-----------|
| **`BRIEF.md`** | The task. Read this first. |
| **`proposal-schema.md`** | The data-model spec — the shape your agent's output must match. |
| **`schema.json`** | The same contract, machine-readable (JSON Schema). |
| **`challenge-generator.html`** | The proposal tool. Open it in a browser — offline, no login. Your agent feeds it via `window.loadProposal(...)`. |
| **`sample-inputs/`** | Example adviser notes + a voice-note transcript, each paired with an `*-expected.json` reference output. |

## Try it in 60 seconds

1. Open `challenge-generator.html` in any modern browser.
2. Open dev tools (F12) → Console.
3. Run:
   ```js
   window.challenge.example();     // a fully-worked proposal
   window.challenge.models();      // the models you can pass to applyModel
   window.loadProposal({ clientName: "Test", applyModel: "balanced" });
   ```
4. Load a sample by pasting the contents of `sample-inputs/01-expected.json` into
   `window.loadProposal( … )` and watch it render. That's the target your agent
   is aiming at.

Then read `proposal-schema.md` and build the agent described in `BRIEF.md`.
