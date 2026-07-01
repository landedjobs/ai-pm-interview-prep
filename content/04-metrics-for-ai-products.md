# 04 · Metrics for AI Products

*Why one number lies, the metric stack that doesn't, and the divergence alarm a non-AI PM never has to build. Updated 2026-07.*

> [!NOTE]
> **The defining AI-metrics failure** is conflating "the model is good" with "the product is good," and collapsing both into a single "AI quality" score. The thing that makes AI metrics different from a normal engagement-and-retention dashboard: **model quality and product outcome can move in opposite directions, silently.** Your North Star can climb while the model quietly rots on a slice of users — and a flat KPI sheet will never show it.

---

## The metric stack — one dominant metric per layer

Every primary source converges on a **stack**, not a flat list. Pick *one dominant metric per layer* and refuse to average them into a single score.

| Layer | Example metrics | Catches |
|---|---|---|
| **Business / Product** | task completion, deflection, adoption, retention, LTV | is the product used / valued |
| **Product engagement** | DAU/WAU, retention, time-to-task | did behaviour move |
| **Model quality** | hallucination rate, faithfulness, factual correctness, recall, language adherence | is the model improving or silently degrading |
| **System / reliability** | p95 latency, $/task, escalation-to-human rate, error budget | is the runtime trustworthy |

Two mechanisms justify the stack: **each layer catches a different failure class** (a single average hides all three behind each other), and — the AI-specific part — **the layers can diverge** (a UX change pushes adoption up while a model swap drops faithfulness, and the blended "score" looks flat).

> [!WARNING]
> **A single blended "AI Quality Score" is the failure Galileo warns about** — it "creates an illusion of confidence that is unjustified" by averaging away the divergences that matter. A regression-eval pass can hide a 30% drop in language adherence; a language-adherence test can hide a 50% drop in frontier reasoning. Leadership gets a small dashboard, not a lie compressed into one digit.

---

## The divergence alarm — the metric a non-AI PM never builds

The canonical probe (KORE1): *"You ship an assistant and the north-star metric goes up. How would you know the model is quietly getting worse anyway?"*

Model quality can decay on a sub-population (a new language, a new doc type, a provider model update) while your top-line keeps climbing on momentum, marketing, or the 90% of users who are fine. A flat dashboard reports green right up until the churn shows up weeks later.

**The senior fix:** a **held-out quality gate that runs independently of the product metric.** Concretely — a *"Golden-100"* eval on a stratified, held-out set, run weekly, that **blocks model promotion if faithfulness drops more than ~5%**, regardless of what DAU is doing. Pair it with online detectors: an **escalation-to-human rate** (Notion tracks this deflection signal directly), a **human-correction rate**, and **per-slice quality** so a regression concentrated in one cohort surfaces in 24 hours, not 24 days.

> [!TIP]
> If you can't name the divergence detector — *"weekly stratified eval gating promotion on a faithfulness delta, plus per-slice escalation-rate alerts"* — the grader concludes you haven't internalised that AI products fail in a way traditional ones don't.

---

## Named production stacks

**GitHub Copilot @ Accenture (May 2024)** — a real four-layer stack, one metric per layer:

| Layer | Metric | Number | Role |
|---|---|---|---|
| Fit (leading) | same-day onboarding success | 96% | early signal of fit |
| Adoption | participants who adopted | >80% | did behaviour move |
| Model quality | suggestion **acceptance** rate | ~30% | behaviour-as-quality proxy |
| Perception | rated tool "extremely easy" | 43% | lagging sentiment backstop |

The lesson isn't the numbers — it's the **discipline of behaviour over survey**: the 30% acceptance rate is a per-interaction quality signal you read continuously; the 43% survey is a lagging backstop. **Prefer behaviour over survey the moment you have signal, and never let a model-accuracy number substitute for a retention number.**

**Klarna (first month)** — handled **2.3M conversations** (two-thirds of chats), work of ~700 agents, **on par with humans on CSAT**, resolution time **11 min → under 2**, **25% drop in repeat inquiries**. Read it as the support-chatbot archetype: deflection = NSM, repeat-inquiry rate + CSAT = model-quality, resolution time = system. The cautionary edge: headline deflection is easy; *durable quality is the harder claim* — which is exactly why the divergence alarm and per-slice gate matter here, not just a deflection rate.

---

## Metrics that mislead — name the failure each one hides

| Metric | Lies when… |
|---|---|
| **Raw accuracy** | errors are stratified — 85% overall can be 100% on the easy 80% and 25% on the hard 20% (worse than a flat 90%) |
| **Average latency** | the tail is what users feel — quote **p95/p99**, not the mean |
| **Thumbs-up rate** | evaluators carry a documented negative bias toward *hedged* answers (EMBER, Lee et al., NAACL 2025) — a confidently-worded hallucination collects thumbs-up while an honest calibrated answer is penalised |
| **Engagement / message count** | more messages can mean the assistant is *failing* and users are re-asking |

**Bind each model-quality metric to a downstream cost.** A 3% hallucination rate is a rounding error in a brainstorming tool and a lawsuit in a benefits chatbot (Air Canada). The metric stack is incomplete without a one-line cost-of-error statement per layer — it's what turns *"faithfulness dropped 4 points"* into *"escalate now"* vs *"monitor."*

---

## Pre-stacked metric sets per product archetype

So you recall, not invent, in a case round:

| Archetype | Business / NSM | Model quality | System |
|---|---|---|---|
| **RAG assistant** | task-completion | hallucination rate + faithfulness | p95 latency |
| **Recommendation** | conversion of recommended item | precision@k / recall@k | cost per recommendation |
| **Action-taking agent** | successful end-to-end task rate | hallucination + action-reversibility | spend-cap adherence + kill-switch invocation rate |
| **Support chatbot** | tickets deflected | human-correction + escalation rate | p95 latency + $/resolution |

Every archetype adds the **divergence alarm** on top.

> [!TIP]
> **The pattern that wins every metrics question:** refuse the single number → lay out the four-layer stack in 30 seconds → name the dominant metric per layer for *this* product type → describe the held-out divergence detector by name. Weak answers list DAU and revenue and stop.

> [!WARNING]
> **Naming only product-engagement metrics signals you don't understand that AI products degrade silently.** The interviewer immediately probes "and how would you know the model wasn't quietly regressing on a slice?" — and a candidate with no model-quality layer and no divergence alarm has no answer. Always pair the product metric with a model-quality metric *and* an independent, per-slice divergence detector.

---

## 📚 Resources

*Typed, annotated, capped. `📄 paper · 📘 docs · 🎬 video · 📰 article`.*

- 📰 [The AI Product Success Metrics Interview](https://www.news.aakashg.com/p/ai-success-metrics-interview) — Aakash Gupta. The input/output split as the default opening frame.
- 📰 [AI Product Manager Interview Questions 2026](https://www.kore1.com/ai-product-manager-interview-questions-2026/) — KORE1. The divergence-alarm probe and its strong/weak rubric.
- 📰 [LLM Evaluation Metrics: The Ultimate Guide](https://www.confident-ai.com/blog/llm-evaluation-metrics-everything-you-need-for-llm-evaluation) — Confident AI, May 2026. Faithfulness, recall@k, and the model-quality vocabulary.
- 📰 [Best Practices for LLM Evaluation](https://www.databricks.com/blog/best-practices-and-methods-llm-evaluation) — Databricks, Oct 2025. Methods and the offline/online split.
- 📰 [Latency vs Quality Tradeoffs](https://www.statsig.com/perspectives/latency-quality-tradeoffs) — Statsig, Oct 2025. Why p95, not the mean.
- 📄 [Balancing Speed and Accuracy in LLM Systems](https://arxiv.org/html/2505.19481v1) — arXiv, May 2025. The latency × cost × quality triangle, quantified.
- 🎬 [Becoming an AI PM — metrics, evals, and what AI PMs do](https://www.youtube.com/watch?v=E_rNotqs--I) — Aman Khan (Arize), Lenny's Podcast.

---

**Next:** [05 · Evaluation & Responsible-AI Launch](05-evaluation-and-responsible-ai-launch.md) · Back to the [interview-loop map](../README.md#the-ai-pm-interview-loop-map) · Drill the [metrics question bank](../questions/metrics.md)
