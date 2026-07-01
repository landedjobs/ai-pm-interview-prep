<div align="center">

<img src="https://static.b100x.ai/email/landed-wordmark.png" alt="Landed" width="300" />

# AI Product Manager Interview Prep

**The most comprehensive, senior-grade prep for AI PM interviews in 2026** — the loop, the frameworks, 130+ real questions, and worked answers.

[![Stars](https://img.shields.io/github/stars/landedjobs/ai-pm-interview-prep?style=social)](https://github.com/landedjobs/ai-pm-interview-prep)
[![License: MIT](https://img.shields.io/badge/License-MIT-6C2BD9.svg)](LICENSE)
[![Updated](https://img.shields.io/badge/updated-2026--07-00A86B)](#whats-new-2026-07)
[![Questions](https://img.shields.io/badge/questions-130+-6C2BD9)](questions/)
[![Visit Landed](https://img.shields.io/badge/Visit-Landed-6C2BD9?logo=rocket&logoColor=white)](https://landed.jobs)

*Maintained by [Landed](https://landed.jobs) — scout AI-native roles, get **referred**, prep with mock interviews, and land the job.*

</div>

---

## What an AI PM interview tests in 2026

An AI PM interview is a classical PM loop with an **AI-fluency hard gate bolted on**. Across the six standard rounds you're tested on six things: **product sense** (design an AI feature end-to-end), **technical fluency** (LLMs, RAG, agents, evals — *no coding*), **execution & metrics** (prioritization, A/B, hallucination handling), **strategy** (build-vs-buy, defensibility), **behavioral** (the *largest-weight* round, ~35%), and the new **AI-specific cases** (evals, responsible-AI launch, agentic products). The through-line the rubric rewards in every round: **name a specific metric, a specific failure mode, and a specific user.**

The one shift that changes everything: **an AI feature is a distribution, not a binary** — so every answer must absorb hallucination, drift, miscalibration, and cost variance. **Structure is now the floor, not the ceiling** (Exponent, 2026): a flawless CIRCLES answer that never mentions eval or cost-of-error reads as a 2024 PM.

> ⭐ **Star this repo** — it's the deepest, best-sourced AI-PM prep on GitHub, refreshed for 2026.

---

## The AI PM interview-loop map

The six standard rounds, what each tests, the framework to bring, and where to prep it. *This is the map to work top to bottom.*

| Round | What it tests | Framework to bring | Deep link |
|---|---|---|---|
| **1 · Recruiter screen** | motivation, comp, AI literacy | STAR + a genuine "why AI PM" | [behavioral bank](questions/behavioral.md) |
| **2 · Technical phone screen** | LLM / RAG / agent / eval fluency, model selection (no coding) | prompt→RAG→fine-tune→custom hierarchy | [content/01](content/01-ai-technical-fluency.md) · [technical bank](questions/technical-fluency.md) |
| **3 · Hiring manager / AI craft** | AI-fluency depth, prior launches, ambiguity | capability-to-feature map | [content/06](content/06-model-and-vendor-selection.md) · [content/01](content/01-ai-technical-fluency.md) |
| **4 · Product Sense with AI** *(Meta's new round)* | design an AI feature end-to-end with metrics + eval | the [Six Lenses](frameworks/README.md#2-the-six-lenses--ai-product-sense) | [content/02](content/02-ai-product-sense.md) · [product-sense bank](questions/product-sense.md) |
| **5 · Execution / Metrics / AI Technical** | prioritization, A/B, **hallucination handling**, model trade-offs | input/output split + AI-adjusted RICE | [content/04](content/04-metrics-for-ai-products.md) · [ai-specific cases](questions/ai-specific-cases.md) |
| **6 · Behavioral + Strategy + AI ethics** | ownership, stakeholder tension, **responsible-AI launch** | NIST RMF + the go/no-go table | [content/05](content/05-evaluation-and-responsible-ai-launch.md) · [behavioral bank](questions/behavioral.md) |

<sub>Loop shape: Anthropic ≈ 5 panels same day · Microsoft ≈ 5–6 panels + 1 AI-feature design · Meta added "Product Sense with AI" late 2025. 2026 AI-PM comp ≈ **$165K–$390K** base + equity.</sub>

```mermaid
flowchart LR
    A["📞 Recruiter<br/>screen"] --> B["💻 Technical<br/>phone screen"]
    B --> C["🎯 Hiring manager<br/>AI craft"]
    C --> D["🧠 Product Sense<br/>with AI"]
    D --> E["📊 Execution /<br/>Metrics / AI"]
    E --> F["🤝 Behavioral +<br/>Strategy + Ethics"]
    F --> G["🎉 Offer"]
    style D fill:#6C2BD9,color:#fff
    style E fill:#6C2BD9,color:#fff
    style G fill:#00A86B,color:#fff
```

---

## Contents

- **[content/](content/)** — one concept per file, mini-lecture + senior traps *(7 guides)*
  - [01 · AI Technical Fluency](content/01-ai-technical-fluency.md) — LLMs, prompting, RAG, agents/MCP, cost/latency
  - [02 · AI Product Sense](content/02-ai-product-sense.md) — the six lenses, the wedge, trust design, prioritization
  - [03 · Writing AI PRDs](content/03-writing-ai-prds.md) — acceptance criteria for probabilistic output
  - [04 · Metrics for AI Products](content/04-metrics-for-ai-products.md) — the metric stack + the divergence alarm
  - [05 · Evaluation & Responsible-AI Launch](content/05-evaluation-and-responsible-ai-launch.md) — evals, judge biases, launch gates
  - [06 · Model & Vendor Selection](content/06-model-and-vendor-selection.md) — build-vs-buy, routing, $/task
  - [07 · Designing Human-AI Interaction](content/07-designing-human-ai-interaction.md) — the UX of probability
- **[frameworks/](frameworks/README.md)** — the 10 reusable rubrics with worked examples *(CIRCLES, Six Lenses, AI-adjusted RICE, the AI-eval rubric, the AI-feature-design template…)*
- **[questions/](questions/README.md)** — the tiered bank, **130+ questions** with per-option whys + provenance labels
  - [product-sense](questions/product-sense.md) (35) · [technical-fluency](questions/technical-fluency.md) (30) · [metrics](questions/metrics.md) (18) · [ai-specific-cases](questions/ai-specific-cases.md) (30) · [behavioral](questions/behavioral.md) (18)
- **[answers/](answers/README.md)** — **7 worked answers** to AI-specific case prompts against the frameworks
- **[resources.md](resources.md)** — the typed, annotated, license-noted resource hub *(38 sources)*
- **[Contributing](CONTRIBUTING.md)** · [What's new](#whats-new-2026-07) · [FAQ](#faq)

---

## What's new (2026-07)

*Most recent first.*

- **Evals are the #1 new differentiator.** Round 5 now routinely opens with *"walk me through your eval harness — offline + online."* Added [content/05](content/05-evaluation-and-responsible-ai-launch.md) (the three modalities, the ~80/15/5 mix, LLM-as-judge biases) and a [worked eval-harness answer](answers/03-eval-harness-support-assistant.md).
- **Responsible-AI launch gating** now appears in 3 of 5 major guides. Added the NIST-shaped [go/no-go table](content/05-evaluation-and-responsible-ai-launch.md#the-gono-go-table--every-row-owner--binary-criterion), the defense-in-depth rails, and the [60%-safety launch decision](answers/05-launch-gate-60pct-safety.md).
- **Agentic products** are a distinct case now. Added [agent patterns + compound failure](content/01-ai-technical-fluency.md#4-agents--mcp--the-word-everyone-misuses), MCP, the action gate, and the [agentic-shopping-assistant PRD](answers/06-prd-agentic-shopping-assistant.md).
- **Hallucination handling in production** is a verbatim prompt. Added the [detect/mitigate + incident-loop answer](answers/04-hallucination-in-production.md).
- **"I use ChatGPT for PRDs" is now the *wrong* answer** — AI drafting is baseline; the [eval-first PRD](content/03-writing-ai-prds.md) is the moat.

---

## FAQ

**What's the difference between an AI PM and a traditional PM?**
Same five case-round steps, but **every decision is a distribution, not a binary.** An AI PM adds three lenses a traditional PM never needed — **Economics** (marginal cost per call), **System Design** (where the stochastic step sits in a deterministic pipeline), and **Trust & Liability** (the cost of a wrong answer) — plus eval-driven acceptance criteria and the discipline of designing the *recovery* as a feature. See [content/02](content/02-ai-product-sense.md).

**Do I need to code to be an AI PM?**
No. The technical rounds test *fluency, not implementation* — you reason about tokens, RAG vs fine-tuning, agents, and evals in plain language, and you scope and price features. You never write code. [content/01](content/01-ai-technical-fluency.md) is the whole no-code curriculum.

**How technical must an AI PM actually be?**
Technical enough to (1) commit to the `prompt → RAG → fine-tune → custom` hierarchy and name when to deviate, (2) estimate cost in tokens with stated assumptions, (3) design an eval harness and a guardrail table, and (4) reason about compound failure and where the human goes. That's the bar — mechanism-first thinking, not math. See [content/06](content/06-model-and-vendor-selection.md).

**How do you measure an AI feature?**
Split before you name a number: one **input metric** (what users do — thumbs, retry, edit rate), one **output metric** (what the business gets — task success, deflection, retention), and one **guardrail** (a kill-switch threshold). Then add the **divergence alarm** — a held-out, per-slice quality gate that fires when model quality drops even as the North Star climbs. Full detail: [content/04](content/04-metrics-for-ai-products.md).

**How do you handle hallucinations in production?**
Bind it to cost first (a hallucination is legally attributable — *Air Canada*). **Detect** with a grounded-faithfulness judge on sampled traffic + a per-slice divergence alarm + escalation/correction-rate cross-checks (not thumbs-up, which is biased). **Mitigate** with RAG grounding, enforced abstention, confidence-threshold escalation, and a faithfulness output guardrail — and treat incidents distributionally (page on a rate shift, not one sample) with eval write-back. Worked answer: [answers/04](answers/04-hallucination-in-production.md).

**How long should I prepare, and what should I weight?**
Weight by the real round weights (Aakash Gupta, 47 placements): **Behavioral ~35% · Product Sense ~20% · Execution ~15% · Technical Depth ~15% · Presentation ~10%.** Most candidates over-prep product sense and skip behavioral — [start there](questions/behavioral.md) with 2–3 specific AI failure stories.

---

<div align="center">

### Part of the Landed AI-native jobs family

Cross-linked so you can go from *learn the role* → *prep the interview* → *find the job*.

🌐 [**awesome-ai-native-jobs**](https://github.com/landedjobs/awesome-ai-native-jobs) — the umbrella: every AI-native role, salary, and prep guide
🗺️ [**ai-product-engineer-roadmap**](https://github.com/landedjobs/ai-product-engineer-roadmap) — the learn-and-build path into AI product roles
🧠 [**awesome-ai-engineer-interview**](https://github.com/landedjobs/awesome-ai-engineer-interview) — the sibling for AI *engineering* loops
🧩 [**ai-product-engineer-jobs**](https://github.com/landedjobs/ai-product-engineer-jobs) — live AI product-engineer job listings

</div>

---

<div align="center">

### Don't just apply. Get **referred**, get **prepped**, get **Landed**.

[![Get Started](https://img.shields.io/badge/Get%20Started%20Free-→-6C2BD9?style=for-the-badge)](https://landed.jobs)

<sub>Maintained by [Landed](https://landed.jobs) · No affiliation with the companies named. Content MIT-licensed. · Comp and process figures aggregated from public 2026 sources; ranges are approximate.</sub>

<br/>

[![Star History Chart](https://api.star-history.com/svg?repos=landedjobs/ai-pm-interview-prep&type=Date)](https://star-history.com/#landedjobs/ai-pm-interview-prep&Date)

</div>
