# 02 · AI Product Sense

*Classical product sense executed under probability — the six lenses, the five-step case playbook, and the trust design that separates a senior signal from a 2024 PM. Updated 2026-07.*

> [!NOTE]
> **The thesis.** AI product sense is **not a new discipline** — it is classical product sense where *every decision is a distribution, not a binary*. The same five case-round steps apply (clarify, motivate, segment, find the problem, design a v1), but every answer must absorb model failure modes: hallucination, distribution shift, miscalibration, cost variance. Marty Cagan is blunt — the role becomes *more essential, not less*, and *virtually all PMs will need to be AI PMs*.

---

## The six lenses — three have no classical equivalent

Run any "design an AI feature for X" prompt through all six (Aakash Gupta):

| Lens | Classical equiv? | The question it forces |
|---|---|---|
| **User Reality** | yes | what job, what frequency, what severity |
| **Model Behavior** | partial | reliable capability vs demo-magic |
| **Economics** | **NO — the AI delta** | marginal cost/call; gross margin per use |
| **System Design** | **NO — the AI delta** | is the LLM one fuzzy step inside a deterministic pipeline? |
| **Trust & Liability** | **NO — the AI delta** | cost of a wrong answer (annoying vs lawsuit) |
| **Go-to-Market** | partial | which segment tolerates probabilistic quality |

**Name the three delta lenses out loud.** Most candidates only run User Reality + GTM and produce a non-AI answer wearing an AI costume.

- **Economics** — every call has a *marginal cost*, so a feature that was free to compute in a SaaS app is now a per-use COGS line. Reason in **gross margin per use**: `$0.04/call × 10M/day = $400k/day` of inference can flip a "great idea" into an unaffordable one. This is why Intercom routes between GPT-4/3.5 per scenario, and why Notion serves a smaller fine-tuned model on the hot path.
- **System Design** — the senior pattern is almost never "the LLM does everything"; it's **the LLM as one stochastic step inside an otherwise deterministic pipeline**. Deterministic retrieval/tools fetch facts, the model reasons over them, deterministic validators check the output. Perplexity's citations, a RAG ACL filter, an agent's tool calls are all this pattern.
- **Trust & Liability** — an annoying reply and a *lawsuit-prone medical-advice* reply have wildly different cost tails. Severity must price the **worst case, not the average.**

> [!TIP]
> **A test for whether you're doing AI product sense or just product sense:** could your answer be implemented with a deterministic `if/else` and a database? If yes, you haven't engaged the probability. The AI delta lives in the parts your answer *can't* make deterministic — the fuzzy reasoning step, its cost per call, and the cost of it being wrong.

---

## The five-step case playbook, AI-upgraded

| Step | Box | Classic goal | AI upgrade |
|---|---|---|---|
| Clear communication | 3–5m | state assumptions | flag every assumption the model makes stochastic |
| Product motivation | 3–5m | why it matters | why the **AI variant beats a non-AI baseline** |
| Segmentation | 8–10m | reach vs underserved | **"underserved" = tolerates probabilistic quality** |
| Problem ID | 8–10m | frequency × severity | **severity prices the tail** (cost of a wrong answer) |
| Solution / v1 | 8–10m | impact vs effort | pick model class + eval harness + graceful failure |

The two highest-leverage moves: **segmentation** ("underserved" now means a beta-friendly prosumer, an indemnified B2B user, or a power user inside a feedback loop) and **problem identification** (name the failure mode *before* you design the UX — is a wrong answer a user-input, model-system, or stale-world problem? — then design the recovery *as a feature*).

**The wedge is how you apply a commodity capability, not the model itself.** AI product sense is *"the ability to correctly anticipate what will be truly impactful for users and also feasible with AI"* (Tal Raviv & Aman Khan). Perplexity is the template: not "an AI that answers questions," but *every answer must come from a source with domain authority* — turning an answer from an opinion into a **source of truth**. The capability (generation) is commodity; the product call (citations as the trust boundary) is the wedge.

> [!WARNING]
> **Structure is now the floor, not the ceiling (Exponent, 2026).** AI tools make a *structured* answer trivial to generate. A flawless CIRCLES answer that never mentions hallucination, eval, or the cost of being wrong is the single most common way strong-on-paper candidates lose the AI round. Differentiation comes from a non-obvious user, a real tradeoff, and a named cost-of-error — the AI-specific layer *on top of* the framework.

---

## Designing for trust & failure — calibrated, not maximal

The design goal is **"properly calibrated user trust," not maximal trust** (Google PAIR). Over-trust is as much a defect as under-trust — a user who believes the system while it's hallucinating absorbs the *full* error cost. And **explain for understanding, not completeness** — the citation a user can check beats a model-internals essay they can't.

**The PAIR failure taxonomy — classify, then design the recovery:**

| Failure type | Who/what is wrong | Product response |
|---|---|---|
| **User error** | bad input from the user | prompt repair, inline guidance, clarifying question |
| **System error** | model failed on a reasonable input | fallback, temperature 0, route to a stronger model, **REFUSE** ("I don't know") |
| **Context error** | the world changed (drift, stale docs) | retrieval grounding, "source of truth" prompt, stale-data warning |

*"We'll add error handling"* is the junior answer; *"this is a context error, so we ground + warn on staleness"* is not.

**Refusal is a feature.** A RAG system that *cannot* say "I don't know" will confidently hallucinate on every out-of-corpus question. Anthropic's guidance: return **"Unknown"** on insufficient information. Every trust mechanism — citations, abstention, confidence thresholds, human-in-the-loop — is a **coverage trade**: the system answers *fewer* questions in exchange for being trustworthy on the ones it does answer. The senior question is never "how do we maximize trust?" but "how much coverage are we willing to spend, on which segment, to earn calibrated trust?"

**Surfacing uncertainty — the calibration UI**, cheapest to richest: **(1) provenance** (citations), **(2) hedging language** ("based on the docs I found…"), **(3) graded states** (a high-confidence answer, a "best guess," and an "I don't know" are *visibly different* UI states), **(4) human handoff** at low confidence / high stakes.

> [!WARNING]
> **Raw model confidence is not calibrated probability.** A model's token-likelihood or self-reported "90% sure" doesn't mean it's right 90% of the time — models are routinely over-confident. If you surface a confidence number, you owe a **calibration check** (does "90% sure" correspond to ~90% correct on the eval set?) before it ships. Otherwise prefer provenance and graded states.

**Close the loop.** Thumbs up/down is **RLHF-grade labeled data** — *"your customers are your RLHF annotators"* (Redpoint). Route corrections into the eval set and gate the next model change on them; don't just show a satisfaction dashboard.

**Four trust levers — a serious product uses several:** **framing** (Notion's "isn't perfect" beta sets the prior before the first error), **UI** (citations, graded states), **contracts** (Adobe Firefly's "commercially safe" IP indemnification moves residual risk off the customer), **platform** (Salesforce's Einstein Trust Layer). Match the lever to the segment: a prosumer beta calibrates with framing + UI; an enterprise buyer needs contracts + a trust layer.

---

## Prioritization under uncertainty

Classic RICE still applies, but AI adds three modifiers:

| Factor | Classic | AI-adjusted |
|---|---|---|
| Reach | users affected | users × distributions reliably covered |
| Impact | 1–3 discretionary | 1–3 × confidence-weight |
| Confidence | gut call | **eval pass-rate on real-user traces** |
| Effort | eng units | eng × model-cost/call × tail-risk fix |
| **Cost-of-error** | *(implicit)* | **EXPLICIT: liability + recovery cost (new row)** |

The new row is the point: **a feature with huge reach and a catastrophic tail scores *lower* than a smaller feature whose cost-of-error is bounded** — because reach *multiplies* a bad tail. Klarna had enormous reach and shipped anyway; the tail found it.

**Coverage** is the modifier teams forget — the fraction of inputs the feature handles *gracefully* (answers well *or* fails well: clarifies, abstains, hands off). Narrow-but-graceful beats broad-but-brittle in early AI products.

**The input/output metric split** (Aakash Gupta) is the default opening frame for any "how would you measure success" question. **Input metrics** measure what users *do* (thumbs, retry/regenerate rate, edit-after-suggestion rate) — Day-1, low-latency, reflect model quality. **Output metrics** measure what the *business* gets (task success, deflection/containment, retention of AI-feature users, ARPU lift) — slower, less noisy. Add a **guardrail metric** (a kill-switch threshold — hallucination rate, safety false-positive rate, p95 latency). **One input, one output, one guardrail** is the senior shape.

**The ship threshold is eval saturation, not 100%** (Anthropic) — ship when the agent passes all *solvable* tasks on the slice you care about, with a guardrail wired and a forgiving segment to absorb the residual. Reconcile the two voices: Redpoint's "just do it" *assumes you have an eval* — you skip product polish, not the eval gate.

**Error budgets** port cleanly from SRE — allocate an acceptable failure rate across **hallucination**, **latency** (p95/p99), and **spend**; inside budget, ship features; when you blow the budget, work stops on new features and goes to reliability. The kill-switch fires when a ramp would exhaust the budget.

**Pick a ship posture explicitly** and name its cost: Beta+transparency (Notion) · Citation-required (Perplexity) · Indemnified (Adobe) · Trust-layer wrapped (Salesforce) · Refusal-as-feature (Anthropic). And **route models per scenario** (Intercom GPT-4/3.5) — RICE applied to model selection by cost-of-error.

> [!TIP]
> **Pruning is the prioritization act.** When the model can generate infinite surface area, focus is the scarce resource. "What would you cut?" is a near-certain follow-up — name the secondary surface you're *deliberately* not building (Redpoint/Tome's "do not build dashboards"), not a grudging trim.

---

## Case studies — the same lens set, five different bets

| Company | The call | Lens(es) |
|---|---|---|
| **Notion Q&A** | launched as an "isn't perfect" beta + a concrete enterprise win (Remote: 10-min lookups → seconds) | Trust (via framing) |
| **Perplexity** | citations as the wedge — "almost never wrong, so you can trust it" | Trust + GTM |
| **Intercom** | route GPT-4/3.5 per scenario | Economics + System Design |
| **Klarna** | claimed a bot did the work of 700 agents; reversed in 2025 when quality fell | **skipped Trust & Liability** |
| **Microsoft Recall** | default-on screen capture, pulled to opt-in after backlash | **trust surface miscalibrated pre-launch** |

The winners each **picked a trust posture explicitly** and made the cost-of-error survivable before the first hallucination. The losers skipped the Trust & Liability lens and let the tail find them. The through-line of the whole track: **in AI, the recovery is the product.**

---

## 📚 Resources

*Typed, annotated, capped. `📄 paper · 📘 docs · 🎬 video · 📰 article`.*

- 📰 [AI Product Sense Is Not the Same as Product Sense](https://aakashgupta.medium.com/ai-product-sense-is-not-the-same-as-product-sense-heres-the-roadmap-to-master-both-9298421915da) — Aakash Gupta. The six-lens roadmap this file is built on.
- 📰 [How to build AI product sense](https://www.lennysnewsletter.com/p/how-to-build-ai-product-sense) — Tal Raviv & Aman Khan (Lenny's). The "gap between capability and a feasible user problem" thesis.
- 📰 [Building AI Product Sense, Part 2](https://www.lennysnewsletter.com/p/building-ai-product-sense-part-2) — Marily Nika (Lenny's), Feb 2026. Meta's new "Product Sense with AI" round; the most-cited 2026 reference.
- 📘 [People + AI Guidebook (PAIR)](https://pair.withgoogle.com/guidebook/) — Google. Calibrated trust, the input/system/context failure taxonomy, graceful failure. *License: Google docs — link + commentary.*
- 📰 [AI Product Management 2 Years In](https://www.svpg.com/ai-product-management-2-years-in/) — Marty Cagan (SVPG). Why the role becomes more essential, not less.
- 📰 [Eight Key Lessons for Rocket-Shipping AI Products](https://www.redpoint.com/content-hub/written/eight-key-lessons-for-rocket-shipping-ai-products/) — Redpoint. "Customers are your RLHF annotators"; resist the feature flood.
- 📰 [The AI Product Success Metrics Interview](https://www.news.aakashg.com/p/ai-success-metrics-interview) — Aakash Gupta. The input/output split, worked.
- 🎬 [Becoming an AI PM — Aman Khan](https://www.youtube.com/watch?v=E_rNotqs--I) — Lenny's Podcast. Product sense, evals, and what AI PMs actually do.

---

**Next:** [03 · Writing AI PRDs](03-writing-ai-prds.md) · Back to the [interview-loop map](../README.md#the-ai-pm-interview-loop-map) · Drill the [product-sense question bank](../questions/product-sense.md)
