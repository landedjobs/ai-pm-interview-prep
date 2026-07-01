# 03 · Writing AI PRDs

*A traditional PRD is a contract for a deterministic system; AI breaks that contract twice. How to write acceptance criteria for probabilistic output, the 12-section structure, and the living document that doesn't rot by month two. Updated 2026-07.*

> [!NOTE]
> **Why the contract breaks.** A traditional PRD writes acceptance criteria as *expected values* — same input, same output. AI breaks this twice: the model is **non-deterministic by design** (the "right" output for one query may differ next run), and quality is observable in **distributions, not pass/fail** (a single bad output isn't a bug; 5% bad outputs in a vertical is a critical regression). An AI PRD extends the standard spec with **eval-driven acceptance criteria, guardrail definitions, model-dependency docs, and living-document architecture** (Ainna).

---

## The core shift: from "output = X" to "the eval passes ≥ threshold"

The universal acceptance shape moves from *"given input X, return Y"* to:

> **"Given the eval set, the model produces a distribution of outputs whose [metric] is at least [threshold] across [slice], refreshed at [cadence]."**

The evals **are** the acceptance criteria — not separate from them. Budget **15–20% of the PRD's length to the eval framework**, not 0%.

> [!WARNING]
> **"I use ChatGPT for PRDs" is the wrong interview answer (Aakash Gupta).** AI drafting is baseline, not a moat — *"40 lines of markdown replaced my 15-page PRD."* What AI *cannot* commoditize is the eval-first DNA: the labeled gold set, the numeric thresholds, the guardrail table, the calibrated rubric, the decision log. Copying a template's structure without that DNA produces a fancy PRD that **tests nothing**.

Senior PMs lean toward **terseness in the main spec and depth in a sibling eval-set doc**: a ~2–3 page narrative PRD that *links* to a 3–5 page eval specification.

---

## The 12-section structure (synthesized from leading templates)

Three independently-authored practitioner templates (Ainna's 3-tier pyramid, Miqdad Jaffer's 9 sections at OpenAI, Product School's 5) converge on the same backbone. Four clusters:

```
STRATEGIC FOUNDATION   TL;DR | Problem & Customer | Success Metrics | Scope
AI-SPECIFIC LAYER      Eval Framework | Quality Bar (numeric) | Guardrails (input+output)
                       | Model & Data Strategy | Responsible AI
OPERATIONAL PLAN       Monitoring & Drift | Failure Modes & Fallback | GTM
LIVING EPILOGUE        running log: Date | Decision | Reason | Reversible?
```

Two design choices to defend:

1. **Quality Bar (numeric) is separate from Success Metrics.** Success metrics measure *business* outcomes; quality bars measure *model behavior* against a labeled eval set. Conflating them is the single most common anti-pattern.
2. **The Living Epilogue is non-negotiable.** Requirements evolve as fast as the model under them — a frozen PRD is a *wrong* PRD by month two.

**Token budget (senior default):** ~2–3 pages narrative (TL;DR + Problem + Outcome + Scope) · ~3–5 pages AI-specific · ~1 page operational · living log updated weekly.

Keep the three concerns as *discrete* sections with their own owners: the **eval framework** (scientific rigor) is owned with engineering/data science; the **outcome narrative** is owned by the PM up the chain; the **guardrail/fallback table** (engineering tractability) is owned with on-call/SRE.

---

## Acceptance criteria for probabilistic output — the four layers

The **Xenoss four-layer framework** stops a single test from hiding a regression — each layer catches a *distinct* failure mode:

| Layer | Question | Why one test can't see it |
|---|---|---|
| **Business result** | did it move a north-star metric? | accurate but *unadopted* |
| **User behavior** | fewer steps / retries / escalations? | safe but *unhelpful* |
| **Model behavior** | numeric bars on a labeled eval set? | fluent but *hallucinated* |
| **Operational** | latency / error / cost in envelope? | correct but *too slow/costly* |

**The worked example to memorize** (Miqdad Jaffer, Product Lead @ OpenAI — Shopify Auto Write):

> *"The AI must achieve ≥90% accuracy on a labeled test set of 10,000 queries, limit hallucination rates to <2% via RAG integration, and flag inappropriate outputs with 98% precision, validated monthly through human review."*

~270 characters carrying a model-behavior bar (90% / <2%), a guardrail bar (98% precision), and an operational cadence (monthly) — replacing a dozen vague *"the response should…"* statements.

**Drop this block at the top of the AI-specific section:**

```
Eval set:       10,000 labeled queries, refreshed each model upgrade.
Business result: 15% lift in AI-assisted task completion within 180 days.
Model behavior: >=90% accuracy vs gold; <2% hallucination; <3% off-brand tone.
Guardrails:     98% precision on toxicity/PII; 99.5% recall on jailbreak.
Operational:    p95 latency <2.5s; cost/query <$0.04; fallback path 100% pass.
Cadence:        automated sweep daily; human calibration monthly.
```

> [!TIP]
> **The senior tell: replace adjectives with thresholds.** "High quality," "fast," "accurate," "safe" are wishes. *"≥90% on 10k labeled queries, <2% hallucination, 98% precision, p95 <2.5s"* is a contract an SRE can wire into monitoring. **If a line in your acceptance criteria can't be turned into an alert, it isn't acceptance criteria yet.** "Fast isn't a target" — Miqdad Jaffer.

Refound/Lenny add a fifth dimension — **executability**: write at least one acceptance criterion as a **runnable LLM-as-judge prompt** that returns pass/fail on every CI run. *A PRD a CI bot can lint is a PRD that survives contact with reality.*

---

## The eval-set spec — the sibling doc that does the work

The terse main PRD links to a fatter eval-set specification (~3–5 pages) where the rigor lives. **Hamel Husain's integrity rules** define what makes it real:

- **Source dataset preserved** — you can re-run last quarter's set.
- **Rubric calibrated against human review at least quarterly** — the LLM-judge agrees with experts.
- **Metric decomposed by user slice** — a regression in one vertical isn't hidden by a healthy average.

Composition: **production-derived traces + adversarial seeds**, sized ~2–10k cases, sliced by vertical/segment/query type, with graders that are **code-based** (format/grounding) + **model-based** (tone/coverage) + **human** (final bar). Avoid rigid grading; use **partial credit**. Growth rule: **every production incident becomes a permanent regression case.**

---

## Guardrails — the two-direction, two-layer taxonomy

A missing guardrail table is a missing definition of "deployment-ready."

**By direction:**
- **Input guardrails** (act on the request): topic-relevance filters, prompt-injection detection, PII detection, blocklists.
- **Output guardrails** (act on the response): toxicity filters, hallucination detection, format validation, brand-voice compliance, regulatory restrictions.

**By layer** (Fiddler):
- **Gateway-level** (PII, jailbreak, prompt injection) → non-negotiable defaults.
- **Evaluation-level** (groundedness, hallucination, bias) → staged-rollout flags that decide whether a model change can ship.

Track the **false-positive rate** on the safety filter as a first-class metric — a too-aggressive gateway classifier blocks legitimate flows.

---

## The Shopify Auto Write case, end to end

A text generator for merchant product descriptions on GPT-3 (Davinci-003), with **streaming** for latency, **regeneration limits** to control abuse, **content moderation** for Apple's App Store TOS, and a launch metric of **"15% merchant adoption within 180 days."** The PRD required *"robust feedback loops … and human-in-the-loop QA measured by quarterly review cycles"* — the living-document architecture that stops the doc (and the model) from rotting.

> [!TIP]
> **Tie the launch metric to a named persona and a date** (merchants / 180 days), not a generic "increase engagement." A single dated, named-persona target prevents the most common AI org failure — a model that *"feels magical in demo"* and *"languishes in production"* — by making one team accountable to one measurable outcome by a date.

> [!WARNING]
> **Unmeasurable criteria are the #1 red flag in an AI PRD.** *"Should feel helpful"* can't be linted, monitored, or regression-tested. Discretize it: *"independently rated 4/5 by a calibrated LLM-as-judge, sampled at 1% of traffic,"* or *"≥90% on a 10k labeled gold set, decomposed by user slice."* If an SRE can't wire it into an alert and a CI bot can't lint it, it's a wish, not acceptance criteria — and it will silently let a regression ship.

---

## The living decision log — keep the PRD true

The synthesized structure ends with a **running decision log** — a table so an exec can scan for the *why* behind each *what*:

| Date | Decision | Reason | Reversible? |
|---|---|---|---|
| 2026-06-14 | RAG over fine-tune for policy answers | freshness + citations + ACL | two-way (swap index) |
| 2026-06-28 | route cheap model on browse, frontier on shortlist | $/task ceiling | two-way (config) |

The **Reversible?** column is the quiet senior touch — it tells the room which bets are cheap to unwind (one-way vs two-way doors) and therefore which don't need a long debate. When an exec asks *"why this model and not that one?"*, the log answers in **15 seconds instead of 15 minutes**.

---

## 📚 Resources

*Typed, annotated, capped. `📄 paper · 📘 docs · 📰 article`.*

- 📰 [A Proven AI PRD Template (Miqdad Jaffer @ OpenAI)](https://www.productcompass.pm/p/ai-prd-template) — Product Compass. The Shopify Auto Write worked example and the acceptance-criteria sentence to memorize.
- 📰 [AI PRDs: Everything You Need to Know](https://www.news.aakashg.com/p/ai-prd) — Aakash Gupta, Aug 2025. Why AI drafting is baseline and the eval is the moat.
- 📰 [How Do You Write a PRD for AI Products?](https://ainna.ai/resources/faq/ai-prd-guide-faq) — Ainna. Eval-driven acceptance, guardrail taxonomy, the 3-tier pyramid.
- 📰 [Write a PRD for a Generative AI Feature](https://www.reforge.com/guides/write-a-prd-for-a-generative-ai-feature) — Reforge, Mar 2026. The widely-cited 6-section AI-PRD template.
- 📰 [LLM Evals: Everything You Need to Know](https://hamel.dev/blog/posts/evals-faq/) — Hamel Husain. The eval-as-acceptance-criterion integrity rules (preserved set, quarterly calibration, slice decomposition).
- 📰 [AI Guardrails Metrics](https://www.fiddler.ai/articles/ai-guardrails-metrics) — Fiddler AI. Gateway vs evaluation-level guardrails.
- 🎬 [Why AI evals are the hottest new skill](https://www.youtube.com/watch?v=BsWxPI9UM4c) — Hamel Husain & Shreya Shankar (Lenny's). The "eval judge that runs constantly" framing.

---

**Next:** [04 · Metrics for AI Products](04-metrics-for-ai-products.md) · Back to the [interview-loop map](../README.md#the-ai-pm-interview-loop-map) · Drill the [product-sense question bank](../questions/product-sense.md)
