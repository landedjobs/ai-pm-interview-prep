# 🧩 AI PM Interview Frameworks

*The reusable structures interviewers listen for — with worked examples. Structure is the **floor** in 2026 (Exponent); these are the scaffold you build the AI-specific signal on top of. Updated 2026-07.*

> [!NOTE]
> Every framework here is applied across many original [worked answers](../answers/). Learn the shape once, then apply it to any prompt. The through-line the rubric rewards in *every* round: **name a specific metric, a specific failure mode, and a specific user.**

**Jump to:** [CIRCLES](#1-circles--the-product-design-scaffold) · [The Six Lenses](#2-the-six-lenses--ai-product-sense) · [The Five-Step Case Playbook](#3-the-five-step-case-playbook-ai-upgraded) · [AI-Adjusted RICE](#4-ai-adjusted-rice--prioritization-under-uncertainty) · [The Input/Output Metric Split](#5-the-inputoutput-metric-split) · [The AI-Eval Rubric](#6-the-ai-eval-rubric) · [The AI-Feature-Design Template](#7-the-ai-feature-design-template) · [Anthropic Agent Patterns](#8-anthropic-agent-patterns) · [PAIR Failure Taxonomy](#9-the-pair-failure-taxonomy) · [NIST AI RMF](#10-nist-ai-rmf--responsible-launch)

---

## 1. CIRCLES — the product-design scaffold

The default whiteboard structure for "design a product / feature." Seven steps: **C**omprehend the situation → **I**dentify the customer → **R**eport the customer's needs → **C**ut through prioritization → **L**ist solutions → **E**valuate trade-offs → **S**ummarize.

> [!WARNING]
> **CIRCLES alone is a 2024 answer.** In 2026, AI tools generate a structured answer trivially, so a flawless CIRCLES pass that never mentions hallucination, eval, or cost-of-error *loses* the round. Use it as a starting frame, then layer the [Six Lenses](#2-the-six-lenses--ai-product-sense) on top — that AI-specific layer is where the senior signal lives.

---

## 2. The Six Lenses — AI product sense

Run any "design an AI feature for X" prompt through all six. **Three have no classical equivalent — name them out loud.**

| Lens | The question | Classical? |
|---|---|---|
| User Reality | what job, frequency, severity | yes |
| Model Behavior | reliable capability vs demo-magic | partial |
| **Economics** | margin per use — "is this margin-positive at scale?" | **AI delta** |
| **System Design** | where does the stochastic step sit in a deterministic pipeline? | **AI delta** |
| **Trust & Liability** | cost of a wrong answer — annoying vs costly vs dangerous | **AI delta** |
| Go-to-Market | which segment tolerates probabilistic quality | partial |

**Worked mini-example — "design an AI assistant that drafts replies to patient messages for a clinic."** First move: name the **cost-of-error tail** (a wrong medical reply is *dangerous*, not just annoying — Trust & Liability). That single call forces every later decision: high eval rigor, a human-in-the-loop approval gate, refusal on anything outside a safe scope. Then Economics (route cheap on triage, frontier on the draft), System Design (retrieve the patient's chart via tools; the model only drafts), GTM (an in-clinic staff-assisted beta, not patient-facing solo advice). Full version in [answers/01](../answers/01-ai-feature-clinic-messaging.md).

Full detail: [content/02 · AI Product Sense](../content/02-ai-product-sense.md#the-six-lenses--three-have-no-classical-equivalent).

---

## 3. The Five-Step Case Playbook (AI-upgraded)

| Step | Time | AI upgrade |
|---|---|---|
| Clear communication | 3–5m | flag every assumption the model makes stochastic |
| Product motivation | 3–5m | why the AI variant beats a non-AI baseline |
| Segmentation | 8–10m | "underserved" = *tolerates probabilistic quality* |
| Problem ID | 8–10m | severity prices the **tail** (cost of a wrong answer) |
| Solution / v1 | 8–10m | pick model class + eval harness + graceful failure |

---

## 4. AI-Adjusted RICE — prioritization under uncertainty

| Factor | Classic | AI-adjusted |
|---|---|---|
| Reach | users affected | users × distributions reliably covered |
| Impact | 1–3 | 1–3 × confidence-weight |
| Confidence | gut call | **eval pass-rate on real-user traces** |
| Effort | eng units | eng × model-cost/call × tail-risk fix |
| **Cost-of-error** | — | **EXPLICIT new row: liability + recovery cost** |

**The new row is the point:** a feature with huge reach and a catastrophic tail scores *lower* than a smaller feature with bounded cost-of-error, because reach *multiplies* a bad tail.

**Worked mini-example.** Feature A: huge reach, but a wrong answer could expose customer financial data. Feature B: modest reach, bounded harm, eval pass-rate 92%. → **B can rank higher** — the cost-of-error row down-weights A's reach-multiplied catastrophic tail.

---

## 5. The Input/Output Metric Split

The default opening frame for any "how would you measure success?" question. Split *before* naming a number:

- **Input metrics** — what users *do* (thumbs, retry/regenerate, edit-after-suggestion). Day-1, low-latency, reflect model quality.
- **Output metrics** — what the *business* gets (task success, deflection, retention of AI-feature users, ARPU lift). Slower, less noisy.
- **Guardrail metric** — a kill-switch threshold (hallucination rate, safety false-positive rate, p95 latency).

**One input + one output + one guardrail** is the senior shape. Then add the **divergence alarm** (a held-out, per-slice quality gate that fires when model quality drops as the NSM climbs). See [content/04](../content/04-metrics-for-ai-products.md).

---

## 6. The AI-Eval Rubric

Two complementary structures.

**The 4-part judge rubric (Productboard / Aman Khan)** — how you write the grader prompt:

| Part | Defines |
|---|---|
| **Role** | who the judge is ("you are a support-quality reviewer") |
| **Context** | what the task was, what the model saw |
| **Goal** | what a good output achieves |
| **Labels** | the terminology/label definitions the score maps to |

**The four grader methods:** human evaluation (ground truth) · LLM-as-judge (scale) · grounded evaluation (output vs a source of truth) · automated assertions (format/schema/SQL-validity).

**The eval-suite portfolio + modality mix:**

```
SETS      regression (~100 CI, frozen) + sampled-prod (100+/2-4wk) + frontier (~20 hand-curated)
MODALITY  ~80% deterministic assertions / ~15% LLM-judge on samples / ~5% human calibration
COVERAGE  by DIMENSION (user type x intent x language x adversarial), not by row count
RAG       score retrieval (recall@k) BEFORE generation (faithfulness)
JUDGE     a biased instrument -> randomize order, length-normalize, different-family judge,
          calibrate vs a human golden set (track the agreement rate, recalibrate quarterly)
```

Full detail: [content/05](../content/05-evaluation-and-responsible-ai-launch.md).

---

## 7. The AI-Feature-Design Template

The one-artifact deliverable a senior AI PM produces — a **capability-to-feature map row** plus its **acceptance-criteria block**.

**The frame gate** (run before a feature earns a row): whose workflow changes → cost of error (reversible/expensive/irreversible/regulated) → capability tier → frequent+painful+AI-shaped? → *how will we know it worked without trusting the demo?* Fail the last and it goes back for an eval plan.

**The map row:**

```
Feature | User job & | Capability | Architecture   | Scope           | Cost &  | Failure   | Eval
        | err-cost   | tier       | (prompt/RAG/   | (in/out/task/   | latency | design    | (metric +
        |            |            |  FT/agent)     |  subject)       |         | (HITL?)   | golden set)
```

**The acceptance-criteria block (probabilistic output):**

```
Eval set:    N labeled cases, refreshed each model upgrade, sliced by [dimension].
Business:    [X]% lift in [metric] within [horizon] for [named persona].
Model bar:   >=[Y]% grounded+correct; <[Z]% hallucination; <[W]% off-brand.
Guardrails:  [P]% precision on PII/toxicity; [R]% recall on jailbreak.
Operational: p95 <[L]s; cost/task <$[C]; fallback 100% pass.
Cadence:     automated daily; human calibration monthly.
```

Replace **adjectives with thresholds** — if an SRE can't alert on it and a CI bot can't lint it, it isn't acceptance criteria. Worked in [content/03](../content/03-writing-ai-prds.md) and [answers/06](../answers/06-prd-agentic-shopping-assistant.md).

---

## 8. Anthropic Agent Patterns

Default to the **lowest-agency design that works**; promote one rung only on eval evidence.

```
prompt  →  workflow  →  agent
             |            |
  prompt chaining      orchestrator-workers (only when you can't enumerate sub-tasks)
  routing
  parallelization
  evaluator-optimizer
```

**The three filters for "should this be an agent?":** agency (multi-step tool use the user wouldn't do), reversibility (can a human cheaply catch a wrong step), value density (worth the orchestrator's error budget). And **compound failure**: `0.95^10 ≈ 60%` end-to-end — measure the whole task, shorten the chain, gate irreversible steps. See [content/01 §4](../content/01-ai-technical-fluency.md#4-agents--mcp--the-word-everyone-misuses).

---

## 9. The PAIR Failure Taxonomy

Classify every failure *before* designing the UX — each bucket implies a different fix:

| Failure | Product response |
|---|---|
| **User error** (bad input) | prompt repair, clarifying question |
| **System error** (model missed a reasonable input) | fallback, refuse ("I don't know"), stronger model |
| **Context error** (world changed) | retrieval grounding, staleness warning |

The goal is **calibrated trust, not maximal trust** — over-trust is a defect. See [content/07](../content/07-designing-human-ai-interaction.md).

---

## 10. NIST AI RMF — responsible launch

The verbal default for the responsible-AI round. Four functions: **GOVERN → MAP → MEASURE → MANAGE.** It gives the *shape*, not the thresholds — write company-specific gates (hallucination floor, jailbreak ceiling) in the same shape, each mapped to an eval case and attached to a **Risk Report** (categories × levels × mitigations × residual × rollback). See [content/05 §4](../content/05-evaluation-and-responsible-ai-launch.md#4-responsible-ai-gates--build-it-in-dont-audit-for-it).

---

## 📚 Sources

- 📰 [CIRCLES Framework Guide](https://productschool.com/blog/skills/circles-framework-guide) — Product School
- 📰 [AI Product Sense Is Not the Same as Product Sense](https://aakashgupta.medium.com/ai-product-sense-is-not-the-same-as-product-sense-heres-the-roadmap-to-master-both-9298421915da) — Aakash Gupta (the six lenses)
- 📰 [The definitive guide to mastering product sense interviews](https://www.lennysnewsletter.com/p/the-definitive-guide-to-mastering) — Ben Erez (the five-step playbook)
- 📰 [AI Evals for Product Managers](https://www.productboard.com/blog/ai-evals-for-product-managers/) — Productboard (the 4-part rubric)
- 📘 [Building Effective AI Agents](https://www.anthropic.com/research/building-effective-agents) — Anthropic (agent patterns)
- 📘 [People + AI Guidebook](https://pair.withgoogle.com/guidebook/) — Google PAIR (failure taxonomy)
- 📘 [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework) — NIST

---

*Part of [ai-pm-interview-prep](../README.md). Content MIT-licensed.*
