# Worked Answer 04 — Detect and mitigate hallucinations in production

**Prompt (AI-Technical / Metrics round, verbatim 2026):** *"How would you detect and mitigate hallucinations of an LLM feature in production?"* — [KORE1 2026](https://www.kore1.com/ai-product-manager-interview-questions-2026/)

> [!NOTE]
> The weak answer is "use a better model" or "add a filter." The strong answer separates **detect** from **mitigate**, names the **cost-of-error binding** (a 3% hallucination rate is a rounding error in brainstorming and a lawsuit in a benefits chatbot), and closes with the **incident loop**.

---

## First: what is a hallucination here, and what does it cost?

A hallucination is a **fluent, confident claim the model's context never supported** — it fills a gap in the weights with plausibility. Before anything else, bind it to a cost: for a customer-facing policy assistant, an ungrounded confident misstatement is **legally attributable to the company** (*Moffatt v. Air Canada*). That binding sets the rigor of everything below.

---

## Detect

1. **Grounded-faithfulness eval on sampled traffic** — an LLM-as-judge (different model family, calibrated to a human golden set) scores whether each claim is supported by the retrieved context. This is the primary online detector.
2. **A per-slice divergence alarm** — a held-out, stratified faithfulness gate that runs *independently* of the North Star, because deflection/engagement can climb while the model rots on a slice (a new policy, a new language). It fires on a faithfulness delta regardless of what DAU is doing.
3. **Behavioural cross-checks** — rising **escalation-to-human** and **human-correction** rates are truer signals than thumbs-up, which carries a documented bias *toward* confident-but-wrong answers (EMBER). Triangulate sentiment against behaviour.
4. **Citation/existence checks** — for a RAG feature, assert that every cited article/policy actually exists and that the claim maps to it.

## Mitigate

| Lever | What it does |
|---|---|
| **RAG grounding** | put the source text in context so the model answers *from* it, not from the weights — the root fix, since hallucination on private/fresh data is a *grounding gap* |
| **Enforced abstention** | permit and reward "I don't know" / "Unknown" — refusal is the cleanest graceful failure; it bounds the cost of error on out-of-corpus questions |
| **Confidence-threshold escalation** | below a threshold (or on no-match), route to a human instead of guessing |
| **Output guardrail** | a hallucination/faithfulness detector as a gateway rail that blocks or flags an unsupported response |
| **Structured output** | constrain the answer shape so each field is checkable against a source |

Note what's *not* on the list first: "use a bigger model." Model scale reduces but doesn't eliminate ungrounded claims and adds no grounding, citation, or escalation.

## The coverage trade (name it)

Every mitigation — abstention, citations, confidence thresholds — makes the system answer *fewer* questions in exchange for being trustworthy on the ones it does answer. The senior question isn't "how do we eliminate hallucination?" (you can't) but "how much coverage do we spend, on which segment, to earn calibrated trust?"

## The incident loop (when one slips through)

A single wrong answer is **not an incident** — a *rate shift* is (hallucination rate up >10pp WoW). The loop:

```
detect (severity tag) -> triage (which layer: retrieval miss? prompt regression? model swap?)
 -> mitigate (disable a rail / tighten abstention) -> comms (template)
 -> rollback (rehearsed drill) -> WRITE-BACK: the sample becomes a permanent eval case
```

---

## The one-paragraph answer

> *"Bind it to cost first — for a customer-facing assistant a hallucination is legally attributable (Air Canada). **Detect** with a grounded-faithfulness judge on sampled traffic, a per-slice divergence alarm independent of the NSM, and escalation/correction-rate cross-checks (not thumbs-up, which is biased). **Mitigate** with RAG grounding, enforced abstention, confidence-threshold escalation, and a faithfulness output guardrail — naming the coverage trade. And treat incidents distributionally: page on a rate shift, not one sample, and write every failure back into the eval set."*

## Follow-up pushes

- *"Users complain, but your faithfulness score is fine."* → check the divergence between the judge and reality — recalibrate the judge against the human golden set; the score may be miscalibrated.
- *"Won't abstention hurt the coverage metric?"* → yes, deliberately — it's a coverage-for-trust trade; set an explicit abstention rate and review borderline refusals to avoid refusal-washing.

**Framework applied:** [PAIR failure taxonomy](../frameworks/README.md#9-the-pair-failure-taxonomy) · [the metric stack + divergence alarm](../content/04-metrics-for-ai-products.md) · [content/05 §incident loop](../content/05-evaluation-and-responsible-ai-launch.md#5-rollout-rollback-and-the-incident-loop).

*[← Previous](03-eval-harness-support-assistant.md) · [Answers index](README.md) · [Next: launch gate at 60% safety →](05-launch-gate-60pct-safety.md)*
