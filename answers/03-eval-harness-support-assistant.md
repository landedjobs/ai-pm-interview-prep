# Worked Answer 03 — "Walk me through your eval harness"

**Prompt (Execution / AI-Technical round, verbatim 2026):** *"Walk me through your eval harness for a customer-facing support assistant — offline eval set + online measurement."*

> [!NOTE]
> KORE1's strong/weak contrast is the cleanest teaching artifact: **strong answers separate offline from online and name a fallback when the model is wrong; weak answers say "accuracy" and "user feedback."** Lead with the three modalities and the allocation, then the judge, then the calibration and the ship gate.

The feature: a RAG assistant answering policy questions (refunds, baggage, bereavement fares) over a help-center corpus.

---

## 1. The eval suite is a portfolio, not a dataset

Three sets, each catching a different failure:

| Set | Size / cadence | Grader | Catches |
|---|---|---|---|
| **Regression (CI)** | ~100, frozen, per build | deterministic assertions (citation present, policy-id valid, JSON schema, refusal on out-of-scope) | regressions — cheap, reproducible |
| **Sampled-production** | 100+ fresh traces every 2–4 weeks | LLM-as-judge (reference-free) | drift you never wrote a test for |
| **Frontier** | ~20 hand-curated hard cases | judge + human | model differences (which model to ship) |

**Coverage by dimension, not row count:** policy type × language × question intent × adversarial/"should-refuse." A missing category is a *silent confidence error*.

## 2. RAG-specific: evaluate retrieval before generation

Score **recall@k on retrieval first**, then **faithfulness on generation**. A generation metric can't fix a retrieval miss — if the right article wasn't retrieved, no amount of judging the answer helps. And harvest the gold *contexts* from the **production retriever's** runs, not hand-picked passages, or offline scores read higher than reality.

## 3. The modality mix (allocate by cost + reproducibility)

```
~80%  deterministic CI assertions   — regression, cheap, reproducible
~15%  LLM-as-judge on sampled traffic — drift, scales to millions of requests
~5%   human review / golden set       — GROUND TRUTH; calibrates the judge
```

Human-only scoring is *"mathematically impossible for production systems"* (Galileo). A frozen offline set decays. The mix covers both, with the human layer as the small, expensive anchor.

## 4. The judge is a biased instrument — name the mitigations

| Bias | Mitigation |
|---|---|
| position | randomize order; score both orderings; a win counts only if it survives the swap |
| verbosity | length-normalize; penalize padding in the rubric |
| self-preference | judge with a **different model family** than the assistant |
| reference-answer | score reasoning against criteria, not string overlap |

**Calibrate:** humans build a golden set, the judge runs continuously, and I track the **judge-vs-human agreement rate**, recalibrating the rubric quarterly. Humans set the bar; the judge scales it. The judge prompt itself uses the 4-part rubric — Role / Context / Goal / Labels.

## 5. Offline vs online — different questions

- **Offline** (pre-release): golden/regression set → precision/recall/faithfulness → catches *regressions* before ship.
- **Online** (sampled live traces): task-completion, escalation-to-human rate, human-correction rate → catches *drift* you never imagined.

## 6. The fallback and the ship gate

- **Fallback when the model is wrong:** grounded-or-abstain — if faithfulness is low or no article is retrieved, the assistant says "I'm not sure" and escalates. Refusal is a feature, not a defect.
- **Ship gate:** eval saturation on the slice I care about (passes every *solvable* task), with a guardrail (unsupported-claim rate) wired to a kill-switch and a forgiving segment first — **not** a single accuracy number.

## 7. The write-back loop

Every production incident becomes a **permanent regression case** in the CI set. That's what makes the eval get stronger over time instead of decaying.

---

## The one-sentence senior signal (say it unprompted)

> *"An eval suite is a **portfolio** (regression + sampled-prod + frontier), measured across **three modalities** (~80% deterministic / ~15% judge / ~5% human), covered by **dimension**, gated by a judge **calibrated** to a human golden set — and for RAG, I score retrieval before generation."*

## Follow-up pushes

- *"How often do you refresh the golden set, and who owns ground truth?"* → on a cadence, with a *named* domain owner — ground truth is a role, not a vote.
- *"How do you keep the judge from drifting?"* → track judge-vs-human agreement on the golden set; recalibrate quarterly.
- *"Coverage on a minority-language slice?"* → a needle test per language (Notion's pattern); coverage by dimension is the answer, not more rows.

**Framework applied:** [AI-eval rubric + eval-suite portfolio](../frameworks/README.md#6-the-ai-eval-rubric) · [content/05](../content/05-evaluation-and-responsible-ai-launch.md).

*[← Previous](02-ai-feature-recruiting-screener.md) · [Answers index](README.md) · [Next: hallucinations →](04-hallucination-in-production.md)*
