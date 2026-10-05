# Proposal data model — the output contract

Your agent's job is to turn messy human input (adviser notes, or a voice note)
into **one JSON object** that describes an investment proposal, and hand it to
the generator by calling:

```js
window.loadProposal(proposalObject)   // object, or a JSON string
```

The generator merges your object onto a blank proposal, opens it in the editor
and renders it. **Anything you leave out keeps its sensible default** — you only
need to fill the fields the notes actually give you. This document is the full
list of fields the generator understands.

> All data in the tool is fictional (the firm "Meridian Wealth", made-up
> Portfolio Managers, made-up model portfolios). Nothing here is real.

---

## The quickest path (recommended for the baseline)

The proposal is built around a **model portfolio**. If you pick a model, the
generator fills in the asset allocation, portfolio composition, fees and the
investment-objective preset for you. So the easy recipe is:

1. Pick a model with `applyModel` (see model IDs below).
2. Fill in the **client-specific** fields the notes give you — who the client
   is, what they need, how much they're investing, the target return, the
   adviser fee.

```js
window.loadProposal({
  clientName: "Dr Anita Naicker",
  applyModel: "balanced",         // pulls allocation + composition + fees + preset
  introGreetTo: "client",                 // "client" or "advisor"
  needs: "Recently sold her practice; wants R45,000/month income and offshore growth.",
  goalAssessment: {
    clientType: "Individual",
    currency: "ZAR",
    keyFigures: { totalInvestment: "8500000" }
  },
  objective: { targetReturn: "CPI + 5%" },
  fees: { advisorFee: "0.75" }
});
```

You do **not** have to use a model. If the notes describe a bespoke mandate you
can specify `strategy.allocation`, `strategy.composition` etc. by hand (see
below). Explicit fields always win over anything a model pre-filled.

### Discover what's available at runtime

Your agent can read the live dummy data instead of hard-coding it:

```js
window.challenge.models()      // [{ id, label, portfolioName, scope }]
window.challenge.practices()   // [{ id, name, region }]
window.challenge.people()      // [{ id, name, role, title, practiceId }]
window.challenge.blank()       // a blank proposal object — the full shape
window.challenge.example()     // loads a fully-worked example proposal
```

### Model IDs (stable, shared library)

| `applyModel` value | Portfolio name                    | Currency | Profile |
|--------------------|-----------------------------------|----------|---------|
| `balanced`         | Meridian Balanced Portfolio       | ZAR      | A local + global blend with an income base |
| `global-growth`    | Meridian Global Growth Portfolio  | USD      | Predominantly global equity, growth-focused |
| `income`           | Meridian Income Portfolio         | ZAR      | Income-oriented, defensive |

These are the three shared models. Always confirm the live list with
`window.challenge.models()` — it returns the exact IDs. (All model data is dummy.)

---

## Full field reference

Every field is optional. Types are shown; omit anything you don't have.

### Top level

| Field           | Type    | Meaning |
|-----------------|---------|---------|
| `clientName`    | string  | Who the proposal is for. |
| `clientEmail`   | string  | Client email. |
| `introGreetTo`  | string  | `"client"` (default) or `"advisor"` — who the introduction is addressed to. |
| `applyModel`    | string  | Convenience: a model ID to hydrate the whole proposal from. Not stored on the proposal. |
| `introduction`  | string  | Opening letter. Multi-line; bullet lines starting with `•` are fine. |
| `needs`         | string  | The client's needs & objectives, in prose (feeds Goal Assessment). |
| `about`         | string  | "About the firm" blurb. Has a sensible default — usually leave it. |
| `process`       | string  | "Our investment process" text. Has a default — usually leave it. |
| `status`        | string  | `"draft"` (default) or `"complete"`. |

### `goalAssessment` (object)

| Field                    | Type   | Meaning |
|--------------------------|--------|---------|
| `clientType`             | string | e.g. `"Individual"`, `"Trust"`, `"Company"`. |
| `mandateType`            | string | e.g. `"Discretionary"`. |
| `wrapper`                | string | Direct or via a wrapper. |
| `currency`               | string | `"ZAR"`, `"USD"`, `"GBP"`, `"EUR"`. |
| `portfolioModel`         | string | A model ID (alternative to `applyModel`). |
| `investmentStrategy`     | string | Strategy name shown to the client. |
| `rationale`              | string | Why this strategy suits the client (rich text). |
| `strategyBullets`        | string | Strategy characteristics — `•` bullet lines. |
| `keyFigures`             | object | Investment amounts — see below. |

`goalAssessment.keyFigures` (also lives at `strategy.keyFigures`; either is fine):

| Field             | Type   | Meaning |
|-------------------|--------|---------|
| `totalInvestment` | string | Indicative investment value (number as string, no separators, e.g. `"8500000"`). |
| `portfolioValue`  | string | Amount managed by Meridian Private Clients. |
| `portfolioPct`    | string | …as a % of total. |
| `platformName`    | string | Platform name, if any. |
| `platformValue`   | string | Amount on the platform. |
| `incomeValue`     | string | Monthly/annual income drawdown amount. |
| `incomePct`       | string | Income as a % of the portfolio. |

### `objective` (object)

| Field             | Type   | Meaning |
|-------------------|--------|---------|
| `targetReturn`    | string | e.g. `"CPI + 5%"`. |
| `benchmark`       | string | Benchmark description. |
| `riskProfile`     | string | Narrative risk profile paragraph. |
| `strategyChars`   | string | Strategy characteristics — `•` bullet lines. |
| `investmentHorizon` | string | e.g. `"5 years+"`. |

### `strategy` (object) — only if NOT using a model, or to override it

| Field           | Type    | Meaning |
|-----------------|---------|---------|
| `model`         | string  | A model ID (same effect as `applyModel`). |
| `portfolioName` | string  | Strategy/portfolio name. |
| `currency`      | string  | Portfolio base currency. |
| `allocation`    | array   | Asset-class weightings — array of `{ class, key, percent }`. |
| `composition`   | string  | Portfolio composition write-up (prose). |
| `rationale`     | string  | Why this strategy — prose. |

`strategy.allocation` item shape:

```json
{ "class": "International Equity", "key": "intl-equity", "percent": 40 }
```

Recognised `key` values (used for colours/grouping): `sa-equity`,
`intl-equity`, `fixed-interest`, `bonds`, `property`, `cash`, `other`.
Percentages should add up to 100.

### `fees` (object)

| Field         | Type   | Meaning |
|---------------|--------|---------|
| `management`  | string | Management fee % (a model fills this in). |
| `advisorFee`  | string | Adviser fee %. |
| `adminFee`    | string | Admin fee. |
| `brokerage`   | string | Brokerage %. |

### Other objects (rarely needed for the challenge)

- `holdings`: `{ text, rows: [] }` — top-holdings table.
- `replacements`: `{ isReplacement: false, details: "" }`.
- `portfolioManagers`: `[{ name, title, email, phone }]` — usually taken from the
  practice; you can set it explicitly if the notes name a PM.
- `conclusion`: `{ text, declarations: [true, …] }`.
- `representatives`: appendix of people & FAIS categories (defaults are fine).

---

## Worked example

Given adviser notes like:

> "New client, Dr Anita Naicker, sold her medical practice. ~R8.5m to invest.
> Wants about R45k a month income, worried about rand weakness so wants a good
> offshore slice, 7-year horizon. Moderate-aggressive. Put her in the bespoke
> flexible portfolio, adviser fee 0.75%."

a good output object is:

```json
{
  "clientName": "Dr Anita Naicker",
  "introGreetTo": "client",
  "applyModel": "balanced",
  "needs": "Dr Naicker has recently sold her medical practice and has approximately R8.5m to invest. She requires a monthly income of about R45,000, has a seven-year horizon before material drawdown, and is concerned about rand depreciation — a meaningful offshore allocation is important to her.",
  "goalAssessment": {
    "clientType": "Individual",
    "currency": "ZAR",
    "keyFigures": { "totalInvestment": "8500000", "incomeValue": "45000" }
  },
  "objective": {
    "targetReturn": "CPI + 5%",
    "riskProfile": "Moderate-to-aggressive; comfortable with short-term volatility in exchange for inflation-beating real returns over the medium to long term."
  },
  "fees": { "advisorFee": "0.75" }
}
```

`applyModel` fills the allocation, composition and management fee; your object
supplies everything specific to Dr Naicker. See `sample-inputs/` for more
notes→JSON pairs you can test and learn from.
