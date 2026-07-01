# Worked Answer 06 — PRD for an agentic personal-shopping assistant

**Prompt (PRD round, verbatim 2026):** *"Outline a PRD for an agentic personal-shopping assistant."*

> [!NOTE]
> A PRD round for an agent tests four skills — **problem framing, architecture clarity, evaluation rigor, risk/trust.** The difference between a mid and a senior signal is where you spend your tokens: the **AI-specific cluster and the action gate**, not the problem statement. The single fastest way to fail: let the agent purchase autonomously in v1.

---

## 0. Scope first (before drawing any agent loop)

- **User & job?** A busy gift-buyer who knows the occasion but not the product.
- **Autonomy?** It suggests → drafts a cart → **a human confirms the purchase.** Never autonomous checkout in v1.
- **Stakes?** A wrong *recommendation* is embarrassing (recoverable); a wrong *purchase* is costly (irreversible) — so the gate goes at checkout.
- **Architecture?** Single-purpose recommend+compare tool first; multi-agent only if the eval fails on multi-step tasks.

## 1. The product-sense pass (six lenses → wedge)

- **User Reality** — knows the occasion, not the product.
- **Model Behavior** — reliable at constrained recommendation + comparison; **not** at unsupervised checkout.
- **Economics** — cheap model for browsing, stronger only for the final shortlist (Intercom's routing).
- **System Design** — catalog search + price/availability come from **tools**, not the model's memory.
- **Trust & Liability** — the tail is a wrong purchase → autonomy stops at "draft the cart."
- **GTM** — a prosumer/beta segment that tolerates probabilistic quality.

**Wedge:** not "an AI that shops," but **"an assistant that grounds every recommendation in live catalog data with a human-confirmed checkout."**

## 2. The 12-section PRD skeleton (tokens on the AI-specific cluster)

```
STRATEGIC FOUNDATION
  TL;DR ............. 3-line bet: gift-buyer, occasion->product, cart-confirm outcome
  Problem & Customer  "knows occasion, not product"
  Success Metrics ... input: cart-confirm rate, edit rate; output: completed-purchase rate, return rate
  Scope / Non-goals   NO unsupervised checkout in v1 (explicit)
AI-SPECIFIC LAYER
  Eval Framework .... 2k labeled sessions; offline gold + online sampled
  Quality Bar ....... >=90% relevant shortlist; <2% hallucinated product; 98% precision on policy-violation
  Guardrails ........ input: PII, off-topic; output: no fake SKUs, price grounded to tool; ACTION gate = human
  Model & Data ...... route cheap on browse / strong on shortlist; catalog + price via tools, not LLM
  Responsible AI .... no dark-pattern upsell; disclose AI; bias check on brand/price recommendations
OPERATIONAL PLAN
  Monitoring & Drift  daily eval sweep; alert on hallucination-rate + drift
  Failure Modes ..... input/system/context recovery per failure
  GTM & Adoption .... prosumer beta; 15% weekly-active by 180d
LIVING EPILOGUE
  Decision log ...... Date | Decision | Reason | Reversible?
```

**Architecture (Anthropic):** start with a single-purpose recommend-and-compare *tool* grounded in catalog data; introduce multi-step agency only when the eval shows it fails on genuinely multi-step tasks. **The human confirms the buy.**

## 3. The acceptance-criteria block (the quality bar as a contract)

```
On a 2,000-session gold set:
  shortlist relevance >=90% (LLM-as-judge, calibrated quarterly vs human review)
  hallucinated-product rate <2%
  policy-violation precision 98% at the action gate
  p95 session latency <3s; cost/session <$0.08
  fallback path 100% pass; refreshed each catalog or model update
```

Note the input/output split, a guardrail bar, an operational envelope, and a cadence — **no adjectives.**

## 4. The failure taxonomy → recovery

- **Input error** (vague — "a gift for my dad") → ask a clarifying question.
- **System error** (invented a product that doesn't exist) → ground in tool results; abstain on no-match; fake-SKU output guardrail.
- **Context error** (price/stock changed) → re-fetch at action time; never trust a cached price.

## 5. The action gate (the agentic-PRD tell)

Everything up to "draft the cart" is probabilistic and recoverable; the **irreversible step (charging a card) is gated by human confirmation.** This generalizes Tome's outline-gate — convert an open-ended agentic task into an *iterative contract the user accepts.* A candidate who lets the agent purchase autonomously in v1 has mis-priced the cost-of-error tail — the fastest way to fail this round.

## 6. Debug readiness (the round often pivots to an incident)

*"Three weeks after launch, recommendation quality dropped 15% — debug it."* Name the [debug ladder](07-debug-ladder-quality-regression.md) out loud, and note the likeliest culprit is a **silent provider model update** — safeguard with pinned model + prompt versions and an eval gate on every change.

---

## The reusable shape (the point of the capstone)

Swap in a **trip-planner** (action gate = booking) or an **enterprise coding agent** (action gate = "open a PR, human reviews before merge") and the skeleton holds: scope → six lenses → 12-section PRD with the action gate → debug ladder. That's a senior answer for an agent you've never seen.

**Framework applied:** [12-section AI PRD](../frameworks/README.md#7-the-ai-feature-design-template) · [Anthropic agent patterns](../frameworks/README.md#8-anthropic-agent-patterns) · [content/03](../content/03-writing-ai-prds.md) + [content/01 §agents](../content/01-ai-technical-fluency.md#4-agents--mcp--the-word-everyone-misuses).

*[← Previous](05-launch-gate-60pct-safety.md) · [Answers index](README.md) · [Next: the debug ladder →](07-debug-ladder-quality-regression.md)*
