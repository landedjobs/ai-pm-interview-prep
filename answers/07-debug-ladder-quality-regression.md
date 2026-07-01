# Worked Answer 07 — "Quality dropped 15% three weeks post-launch — debug it"

**Prompt (Execution / incident round, verbatim 2026):** *"Three weeks after launch, your AI feature's quality dropped 15%. You shipped nothing. Debug it."*

> [!NOTE]
> **The score is decided in the first sentence.** "Let me check the logs" brands you as junior. "I'd work a ladder of suspects, budget time on each, and update stakeholders as I go" brands you as someone who's been on-call for a probabilistic system. Name the ladder *before* you touch any single rung.

---

## The prerequisite (say it up front)

You cannot debug what you didn't instrument. This answer assumes **pinned model + prompt versions and a gold-set eval suite** — the prerequisite, not the afterthought. If they're missing, that's finding #1 and the process fix.

## The debug ladder (cheapest → deepest)

| # | Suspect | Diagnostic action |
|---|---|---|
| 1 | **Measurement error** | replay the eval pipeline; check labeler/judge drift — *is quality actually down, or is the metric?* |
| 2 | **Data drift** | compare the input distribution, last 7d vs prior 30d — new query types, new user mix |
| 3 | **Prompt / policy change** | diff the system prompt + tool definitions between versions |
| 4 | **Model version change** | check deployment IDs — **did the provider silently update the model?** Is a rollback available? |
| 5 | **Rate limit / quota** | inspect token/sec caps — are calls throttling or truncating under load? |
| 6 | **User-behaviour shift** | segment new vs returning; did a campaign change the mix? |
| 7 | **Infrastructure** | tool/vector-DB latency, embedding-service health, a retrieval re-index |

I budget time per rung and state my stakeholder cadence as I go: **~1h to surface a hypothesis, ~4h to root cause, ~24h to a fix**, with updates every ~2 hours.

## The single likeliest culprit

You **shipped nothing, yet quality fell.** That's the signature of a **silent provider-side model update** (rung 4) or **drift** (rung 2) — not random noise (a sustained drop with no deploy isn't noise). Re-run the gold set against the pinned version to confirm.

## The fix in scope

1. **Roll back** to the pinned model + prompt version (the rehearsed kill switch, executed in <15 min).
2. **Re-run the gold set** to confirm the rollback recovered quality.
3. **Add the failure cluster** as a permanent regression case in the eval set — write-back, so the same regression can't ship silently again.

## Prevent recurrence (they will ask)

Not "monitor more." A *named guardrail*:

- **Pin model + prompt versions** and gate every change on an eval.
- A **hallucination-rate (or faithfulness) kill-switch** wired to the ramp.
- A **drift alarm** with an explicit threshold on the input distribution.
- A **per-slice divergence alarm** so a regression in one cohort surfaces in 24 hours, not 24 days.

## What makes this an AI incident, not a normal one

- **Distributional, not binary** — a single wrong answer isn't the incident; the *15% rate shift* is. You triage on statistical-control signals.
- **Legally consequential** (if customer-facing) — an ungrounded confident answer is attributable (Air Canada), so the escalation SLA ties to severity.
- **The fix closes a loop** — the incident sample becomes an eval case; the system gets *stronger*, not just patched.

---

## The one-paragraph answer

> *"First: I can only debug this because we pinned versions and have a gold-set eval — if we didn't, that's finding #1. Then I work a ladder of suspects — measurement, data drift, prompt/policy diff, model-version change, rate limits, user mix, infra — budgeting time on each and updating stakeholders every couple hours. A no-deploy drop most likely means a silent provider model update or drift, so I re-run the gold set against the pinned version, roll back via the rehearsed kill switch, and fold the failure cluster into the eval as a permanent regression case. To prevent recurrence: pinned versions gated on an eval, a faithfulness kill-switch on the ramp, and a per-slice divergence alarm."*

**Framework applied:** the debug ladder ([content/01 §economics/debug](../content/01-ai-technical-fluency.md#5-cost-latency--routing-economics--a-product-problem-you-own)) · pinned versions + eval gate ([content/05](../content/05-evaluation-and-responsible-ai-launch.md)).

*[← Previous](06-prd-agentic-shopping-assistant.md) · [Answers index](README.md)*
