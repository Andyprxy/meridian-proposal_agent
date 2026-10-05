# AI Engineer challenge — the proposal agent

Welcome, and thanks for taking this on. This is a practical take-home meant to
show us how you think and build, not to trick you. Budget **about 4–6 hours** —
if you run out of time, tell us what you'd do next; we care more about your
reasoning than a finished product.

Everything in this pack is **fictional** — the firm "Meridian Wealth", its
Portfolio Managers, and its model portfolios are all made up. There is no real
client or company data anywhere.

---

## The scenario

Portfolio Managers write investment proposals for their clients. They already
have a tool — `challenge-generator.html` in this pack — that builds and renders
a proposal once its fields are filled in. The slow part is the typing: a PM
finishes a client meeting with a page of notes (or a voice note in the car) and
then has to translate that into the tool by hand.

**Your job: build an AI agent that does that translation.** It takes the PM's
notes (or a voice note) and produces a proposal, pre-populated in the tool,
ready for the PM to review and tweak.

---

## How the tool works (the important bit)

The generator exposes one function — this is the seam your agent targets:

```js
window.loadProposal(proposalObject)   // a JS object, or a JSON string
```

You give it an object matching **`proposal-schema.md`** (the data-model spec)
and it builds the proposal, opens it and renders it. Open
`challenge-generator.html` in a browser and try it from the dev-tools console:

```js
window.challenge.example();     // see a fully-worked proposal
window.challenge.models();      // list the model portfolios you can use
window.loadProposal({ clientName: "Test Client", applyModel: "balanced" });
```

So the shape of the problem is: **messy human text → a valid proposal object →
`loadProposal(...)`.** Read `proposal-schema.md` first — it's the contract your
agent's output must satisfy, and it tells you the shortcut (`applyModel`) that
fills most of a proposal from a single model choice.

---

## What to build

### Baseline (this is a complete, passing submission on its own)

An agent that:

1. Takes **typed adviser notes** as input (plain English — see `sample-inputs/`).
2. Uses an LLM to extract the details and produce a proposal object that
   conforms to `proposal-schema.md`.
3. Loads it into the tool via `window.loadProposal(...)` so the proposal renders.

Use **any** LLM or agent framework you like — this is model-agnostic. We run
our production agents in **GitHub Copilot** and **Claude**; you can develop
against whatever you have access to (OpenAI, Claude, Copilot, a local model —
your choice). What matters is that your output conforms to the contract.

How you connect the agent to the tool is up to you — a small web page that calls
`loadProposal`, a script that writes a JSON file you paste in, a browser
extension, whatever is cleanest. Keep it simple.

### Bonuses (reach for these only once the baseline works)

- **Voice notes.** Accept an audio file (or live mic), transcribe it, and run the
  same pipeline. There's a sample transcript in `sample-inputs/` to develop
  against.
- **WhatsApp.** Let a PM send notes (or a voice note) over WhatsApp and get the
  proposal back. A sandbox (Twilio / Meta) is fine — you don't need a live
  number.
- **Handling ambiguity.** Real notes are incomplete. A strong agent notices what's
  missing (amount, horizon, income) and either asks a follow-up question or
  clearly flags the gaps rather than inventing numbers. See `03-notes-sparse.txt`.

---

## What to submit

- Your **code**, in a git repo, with a short **README** explaining how to run it
  and which model/framework you used.
- A **2–3 minute screen recording** (or a few screenshots) of it working end to
  end: notes in → proposal rendered in the tool.
- A few sentences on **what you'd do next** with more time, and anything you'd
  change about your approach.

Do **not** put any real personal or company data in your submission — keep
everything synthetic, as this pack does.

---

## Rules & tips

- **Don't fabricate financial figures.** If the notes don't give an amount or a
  horizon, leave it blank or flag it — never guess a number for a client.
- Start by reading `proposal-schema.md` and running the samples in
  `sample-inputs/` through `loadProposal` by hand, so you know what "good output"
  looks like before you wire up the agent.
- The tool runs fully offline — no login, no internet, no accounts. Just open the
  HTML file.
- Ask us questions. Knowing when to ask is part of the job.

Good luck — we're looking forward to seeing how you approach it.
