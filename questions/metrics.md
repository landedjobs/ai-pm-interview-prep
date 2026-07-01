# Metrics — Question Bank

*18 questions on the input/output split, the metric stack, the divergence alarm, and the metrics that mislead. Multiple-choice checkpoints (✅ correct; every option carries a why) + real-loop questions with provenance. Updated 2026-07.*

Legend: ✅ correct · ✅ **Reported** · 🔮 **Representative** · 🟢🟡🔴 difficulty.

Study first: [content/04 · Metrics for AI Products](../content/04-metrics-for-ai-products.md) · [content/02 §Prioritization](../content/02-ai-product-sense.md#prioritization-under-uncertainty).

---

## The metric stack & the divergence alarm

**Q1 🟡 — Your assistant's weekly active users are up 12% QoQ and leadership wants to declare victory. What does a senior AI PM check first?**

- ✅ Whether model quality diverged from the rising NSM — run a held-out, per-slice quality gate (e.g. faithfulness) independent of WAU — *KORE1's "north-star up, model quietly worse" probe. WAU can climb on momentum while the model regresses on a slice.*
- ▫️ Whether the 12% is statistically significant — *Confirms the product metric moved; says nothing about silent model degradation.*
- ▫️ Whether marketing spend explains the lift — *Attribution matters for growth, not the AI-specific divergence risk.*

**Q2 🔴 — A teammate proposes a single blended "AI Quality Score" (0–100) as the feature's headline metric. Best response?**

- ▫️ Approve it — one number is easiest for executives — *A blended score averages away divergences — Galileo's "illusion of confidence."*
- ▫️ Approve it but only if computed by an LLM judge — *The grader being an LLM doesn't fix collapsing layers into one number.*
- ✅ Replace it with a stack — one dominant metric per layer (business / model-quality / system) plus a divergence alarm — *Layered metrics surface which failure class moved.*

**Q3 🔴 — Your support bot's thumbs-up rate is high, but escalations to human are also rising. Most likely explanation?**

- ▫️ The thumbs-up rate is wrong and should be ignored — *It's not wrong, it's biased.*
- ✅ Confident-but-wrong answers collect thumbs-up while genuinely failing — cross-check against escalation/correction rate and a held-out faithfulness eval — *EMBER documents the bias against hedged answers; rising escalations are the truer signal.*
- ▫️ Users are happy but lonely, so they escalate to chat with a human — *A cute story, not a metrics explanation.*

**Q4 🟡 — Define success metrics for an action-taking AI agent that can send emails and make purchases. Strongest stack?**

- ▫️ DAU and revenue — the same metrics as any feature — *Ignores AI-specific risks (irreversible actions, hallucinated steps); no model-quality or safety layer.*
- ✅ Successful end-to-end task rate (NSM) · hallucination + action-reversibility (quality) · spend-cap adherence + kill-switch invocation rate (system) · plus a divergence alarm — *The system layer tracks the safety-critical levers action-taking introduces.*
- ▫️ Just measure actions per session — *An engagement proxy that can rise when the agent is failing and retrying.*

**Q5 🟢 — Your dashboard shows average latency at 600ms (fine) but users complain the assistant "feels slow." Likely metric mistake?**

- ▫️ The complaints are subjective and can be ignored — *Dismissing the signal is the trap; the mean hides the tail.*
- ✅ You're reporting the mean — switch to p95/p99, where a slow tail (long outputs, cold caches) is what users experience — *Output length and tail behaviour dominate perceived latency.*
- ▫️ Latency doesn't affect AI product perception — *It strongly does — "latency is perceived as quality."*

---

## Defining success as a distribution

**Q6 🟡 — "Our summarizer is 85% accurate on our test set. Do we ship?" Strongest opening?**

- ▫️ Yes — 85% is well above chance and good enough for most users — *The canonical weak answer; a flat number tells you nothing without task, cost-of-error, and fallback.*
- ✅ Refuse the flat number: ask what task, what the cost of a wrong output is, how the 15% is distributed, and what the human-fallback path is — then define a ship gate — *Convert "85%" into a distributional, cost-bound decision.*
- ▫️ No — never ship below 95% accuracy on any AI feature — *A fixed universal threshold ignores that 85% is fine for movie recs and unshippable for medical triage.*

**Q7 🟡 — A PM writes the definition of done as "all eval cases pass." Why push back for an LLM feature?**

- ▫️ Because eval cases are too expensive to run every build — *Cost isn't the issue; the binary framing is.*
- ✅ Because "all pass" is binary, but the feature is a distribution — done should be a target rate on a scoped input class plus a graceful tail and a tripwire — *"Each task has its own success rate"; done is "80% of category X meets bar Y, the rest degrades gracefully."*
- ▫️ Because eval cases can't test non-English inputs — *They can; the flaw is the binary framing.*

**Q8 🟡 — A regex + keyword baseline scores 0.96 precision / 0.95 recall for detecting meeting-request emails. Senior call?**

- ✅ Ship the rule-based baseline; only reach for an LLM if you can show it beats those numbers on the same set — *PAIR's unique-value test: a heuristic that clears the bar makes a non-deterministic, hallucination-prone model a strict downgrade.*
- ▫️ Use an LLM anyway — it'll generalize better to future formats — *A speculative future advantage doesn't justify a harder-to-eval, costlier system today.*
- ▫️ Use an LLM because rules don't scale — *A regex over emails scales fine and deterministically.*

**Q9 🟡 — A stakeholder calls a model "proven" after it passed the demo three times, but each attempt succeeds 70% of the time. How do you frame the launch metric?**

- ▫️ Report pass@1 from the demo — three successes is representative — *Three runs of a 70% task succeed in a row ~34% of the time by chance.*
- ✅ Characterize the distribution: run each task many times, report pass@k across replications plus latency / cost-per-task / error rate — *pass@k with independent trials is the honest metric for a stochastic, retry-allowed system.*
- ▫️ Lower the temperature to 0 and re-run the demo once — *Temperature 0 is greedy, not deterministic, and one re-run doesn't characterize the distribution.*

---

## The eval mix & judge biases

**Q10 🟡 — A PM proposes having the support team manually review every production conversation to score quality. Why reject this as the primary eval?**

- ▫️ Manual review is too subjective to be useful — *Human review is the ground truth that calibrates everything else; the problem is using it as the primary at-scale scorer.*
- ✅ Human-only scoring doesn't scale — reserve humans for a golden set, run LLM-as-judge on sampled traffic, and deterministic assertions in CI — *~80% CI assertions / ~15% judge on samples / ~5% human calibration.*
- ▫️ The support team isn't qualified — *Qualification isn't the blocker; the math of volume is.*

**Q11 🔴 — Your LLM judge does pairwise "A vs B" and a new variant keeps winning. An engineer notices it's always shown as option A. Most likely issue?**

- ✅ Position bias — the first option wins more often; randomize order and score both orderings, counting a win only if it survives the swap — *"A always wins" is an artifact of position, not quality.*
- ▫️ The new variant is genuinely better; ship it — *You can't conclude that until you control for position.*
- ▫️ Verbosity bias — the new variant must be longer — *The symptom (always shown first) points to position bias.*

**Q12 🔴 — You evaluate a GPT-family assistant using a judge from the same GPT family, and scores look great. Senior concern?**

- ▫️ Nothing — same-family judging is the most accurate — *The opposite: a judge favours its own family's outputs.*
- ▫️ Latency — same-family judges are slower — *Speed isn't the issue.*
- ✅ Self-preference bias — the judge favours its own family; use a different-family judge and cross-check against a human golden set — *A systematic bias that inflates scores.*

**Q13 🟡 — A frozen offline eval set has shown green for three months, but users report new failure types. What does this reveal?**

- ▫️ The offline set is wrong and should be deleted — *It's incomplete and stale, not wrong.*
- ✅ A fixed CI set decays — add sampled-production evaluation (LLM-as-judge on 100+ fresh traces every 2–4 weeks) to catch drift, and fold new failures back in — *Frozen sets miss drift; the online layer catches what offline never imagined.*
- ▫️ The model regressed; contact the provider — *Possibly, but the structural lesson is the missing online/drift layer.*

---

## Real-loop metrics questions

> **✅ Reported** unless labeled 🔮. Open with the four-layer stack, name the dominant metric per archetype, then the divergence alarm.

**Q14 ✅ Reported 🔴 — "Model works for 90% of users but poorly for a specific demographic — how would you handle it?"** — [Crosschq](https://www.crosschq.com/blog/ai-product-manager-interview-questions). Segment the harm → quantify (what/how-often/severity) → ship the threshold, not the apology ("we don't auto-launch for group X until the slice metric hits Y; until then shadow-serve/human-review") → name the remediation owner + deadline.

**Q15 ✅ Reported 🔴 — "How would you launch a feature whose predictions are probabilistic and sometimes wrong?"** — [Crosschq](https://www.crosschq.com/blog/ai-product-manager-interview-questions). Name the failure modes → design CX (confidence cues, citations, correction path, escalation) → ship a quality metric + guardrail. See [content/02 §trust](../content/02-ai-product-sense.md#designing-for-trust--failure--calibrated-not-maximal).

**Q16 ✅ Reported 🟡 — "Tell me about your experience running A/B testing for an AI product."** — [IGotAnOffer](https://igotanoffer.com/en/advice/ai-product-manager-interview). Note the AI wrinkle: LLM outputs vary, so you need a larger sample for significance; gate full rollout on the *lower CI bound* crossing the threshold, and consider a shadow phase first.

**Q17 ✅ Reported 🟡 — "A model you launched is underperforming — how do you triage and prioritize?"** — [IGotAnOffer](https://igotanoffer.com/en/advice/ai-product-manager-interview). Name the [debug ladder](../answers/07-debug-ladder-quality-regression.md): measurement → data drift → prompt/policy → model version → rate limits → user mix → infra.

**Q18 ✅ Reported 🔴 — "How would you ensure product defensibility in an AI-saturated market?"** — [IGotAnOffer](https://igotanoffer.com/en/advice/ai-product-manager-interview). The wedge is the *application* + the *data/eval moat*, not the model — the model is the interchangeable part (see [content/06](../content/06-model-and-vendor-selection.md)).

---

*More: [product-sense](product-sense.md) · [technical-fluency](technical-fluency.md) · [ai-specific-cases](ai-specific-cases.md) · [behavioral](behavioral.md) · [index](README.md).*
