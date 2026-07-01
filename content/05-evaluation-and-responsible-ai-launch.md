# 05 · Evaluation & Responsible-AI Launch

*Define "done" for a distribution, build the eval, and gate the launch like a senior AI PM — evals, LLM-as-judge and its biases, responsible-AI gates, rollout, rollback, and the incident loop. Updated 2026-07.*

> [!NOTE]
> **Evals are the #1 new differentiator in the 2026 AI-PM loop.** A deterministic feature is a vending machine: same button, same can, and "done" means the tests pass. An AI feature is a gumball machine — *the same prompt produces different outputs across users, sessions, model updates, and contexts.* The first senior failure mode is copying the deterministic playbook onto a probabilistic product.

---

## 1. Defining success: from a binary spec to a distributional contract

Anthropic states the gap bluntly: *"a task that passed on one eval run might fail on the next"* because **each task has its own success rate.** A single green run tells you almost nothing.

**Eval-driven development** fixes it: build evals *before* the agent can fulfill them — the eval spec lives in the PRD, not after the prototype works. Writing the eval is a forcing function on the requirement itself.

> [!TIP]
> **A "concrete-enough PRD" has a one-paragraph test:** if two engineers can't write the *same* eval spec from your requirement, the requirement is too vague to build. The eval isn't paperwork after the feature — it **is** the specification.

**Gothelf's now-standard definition of done:**

> *"For 80% of inputs in category X, the system returns a response that meets quality bar Y; for the remaining 20%, the failure mode is degraded but not embarrassing."*

Three parts a deterministic spec never had: a **target rate** (80%), a **scoped input class** (category X, not "all inputs"), and an explicit promise about the **tail** (the 20% fails gracefully). The tail clause is the part juniors drop — and the part that keeps you off the front page.

Two consequences: **a single bad output is not a bug — a shift in the failure rate is** (escalate only when the *rate* moves), and the definition has a **shelf life** (Notion re-builds its evals ~every six months as frontier capability shifts the achievable distribution).

**Earn the AI — the rule-based baseline.** PAIR's first question: *"Can AI solve this problem in a unique way?"* If a regex hits 0.96 precision / 0.95 recall on your labeled set, **ship the rule** — a non-deterministic, per-token-cost, hallucination-prone model is a strict downgrade for a problem a rule already solves.

**Use `pass@k`, not a 3-run demo.** A 70%-success task passes 3-in-a-row ~34% of the time by luck. `pass@k` (at least one correct in *k* attempts) with **independent trials** is the honest metric for a retry-allowed system; pair it with latency, token usage, cost per task, and error rate.

**The eval set is smaller than you think.** Notion starts with **10–20 examples per scenario** and grows weekly from production traces; Hamel & Shankar use **100+ purpose-built CI examples** + 100+ fresh traces every 2–4 weeks. **Coverage by dimension beats volume by row** — a missing query category is a *silent confidence error*. Include **negative / "should-refuse" cases**, not just positives.

---

## 2. The three eval modalities and their allocation

Evaluation is not one activity — it's three modalities, each catching a different failure class:

| Modality | ~Share | Tool | Catches |
|---|---|---|---|
| **Offline / CI** | ~80% | deterministic assertions (schema, format, facts) | regressions (cheap, reproducible) |
| **Online** | ~15% | LLM-as-judge on *sampled* live traces | drift you never wrote a test for |
| **Human** | ~5% | expert labels / golden set | **ground truth**; calibrates the judge |

Human-only scoring is *"mathematically impossible for production systems processing millions of requests"* (Galileo). A frozen offline set decays. The two failure modes are symmetric — **offline-only goes stale; human-only doesn't scale** — so the mix covers both, with the human layer as the small, expensive anchor.

**Build the eval set by dimension + failure hypotheses** (Hamel & Shankar's "Lucy" pattern): dimensions let a small set cover a large behaviour space; failure hypotheses force the *negative* cases. Harvest gold *contexts* from your **production retriever's** runs, not hand-picked passages, or offline scores read higher than reality. For RAG, **evaluate retrieval (recall@k) *before* generation (faithfulness)** — a generation metric can't fix a retrieval miss.

**The four grader categories:** human evaluation · LLM-as-judge · grounded evaluation (output vs a source of truth — the no-hallucination check) · automated assertions (length, format, JSON-schema, SQL-validity).

---

## 3. LLM-as-judge — the only thing that scales, and its four biases

The judge that makes the online layer affordable is a **biased instrument, not an oracle.** Naming its biases is a high-signal interview move:

| Bias | What happens | Mitigation |
|---|---|---|
| **Position bias** | option shown *first* wins more in pairwise comparisons | randomize order; score *both* orderings; a win counts only if it survives the swap |
| **Verbosity bias** | longer answer scores higher regardless of quality | length-normalize; penalize padding in the rubric |
| **Self-preference** | judge favours its own model family's outputs | use a **different family** as judge; cross-check |
| **Reference-answer bias** | over-weights surface similarity to a provided reference | score reasoning against criteria, not string overlap |

And the human counter-bias Galileo quantifies: reviewers rate **confident-but-incorrect outputs 15–20% higher** — which is exactly why *neither* humans nor judges are trustworthy alone.

**Calibrate.** Humans build a golden set; the judge runs continuously; you track the **judge-vs-human agreement rate** and recalibrate the rubric quarterly. Humans set and recalibrate the bar; the judge scales it between recalibrations.

> [!WARNING]
> Calling an LLM judge "objective" ends the round. Treat it as a biased instrument — useful only when its order is randomised, its rubric scores reasoning not length, it's from a different family than the model under test, and its agreement with a human golden set is tracked and recalibrated.

---

## 4. Responsible-AI gates — build it in, don't audit for it

**NIST AI RMF** gives the *shape* — **GOVERN / MAP / MEASURE / MANAGE** — but **no quantitative thresholds.** The concrete go/no-go numbers must come from your own policy, written in the same shape (a hallucination floor, a jailbreak ceiling), linked to verifiable artifacts.

**Defense in depth — ≥2 rails per path.** One output content-moderation filter can't stop prompt injection on the input, poisoned retrieval, or unsafe tool calls. You need input + output (plus dialog/retrieval) rails and a **severity-tiered** response (Microsoft's Bug Bar rates prompt-injection-with-exfiltration as *Critical*).

**Execution-rail guardrails for an action-taking agent:** least-privilege scoped permissions per action, human confirmation for irreversible actions, spending caps and per-session rate limits *with numbers*, and a documented + **tested** kill switch with an on-call rotation.

**Red-teaming is only meaningful if you can name:** which public harm **taxonomy** was used, which **methods** (human + automated), what was covered **and not** covered, and whether findings became **permanent eval cases** (write-back). Coverage = taxonomy breadth × methodology diversity × write-back.

**The convergent artifact:** across Anthropic (RSP capability thresholds), Microsoft (Responsible Release Plan), Meta (Llama risk assessments), and OpenAI (Preparedness Framework), the pre-release deliverable is a **Risk Report** — categories × levels × mitigations × residual uncertainty × rollback — attached to the release.

---

## 5. Rollout, rollback, and the incident loop

**Sequence lowest-risk-first: shadow → A/B → canary → full.**

- **Shadow** — run the new model on real traffic but *discard* its outputs; see the real input distribution at zero customer risk (the zero-risk default when confidence is low).
- **A/B** — only when two variants are both shippable; wait for a sample large enough for significance (LLM outputs vary).
- **Canary** — 1–5% of traffic with a kill switch.
- **Full** — gate on the **lower confidence-interval bound** crossing the threshold, not the point estimate.

> [!WARNING]
> **A documented-but-unrehearsed kill switch is a hypothesis, not a kill switch.** Rollback is a **P0 launch deliverable** — a staging drill with a **measured time-to-revert (<15 min)**. "Written down" is not "executed under pressure." Google's Gemini pause is the public lesson.

**AI outputs are legally attributable.** *Moffatt v. Air Canada* made an ungrounded confident misstatement the company's liability. For customer-facing GenAI, gate on **grounding + a confidence-threshold human escalation + citation/policy-existence eval checks** — launch-gate criteria, not nice-to-haves.

**Monitor four families on day one:** quality, cost, latency, and **drift** (semantic/embedding/label drift — the most-often-missing leg; without it you don't see distribution shift until churn appears).

**Incidents are distributional.** A single wrong answer is *not* an incident — a **rate shift** is (hallucination rate up >10pp WoW). The loop: **detect** (severity tag) → **triage** (taxonomy) → **mitigate** (disable a rail) → **comms** (template) → **rollback** (rehearsed drill) → **postmortem** → **write-back** (the incident sample becomes a permanent regression case).

> [!TIP]
> **The behavioural version — "tell me about an AI feature that backfired" — is graded on system-design literacy, not empathy.** Name the *detector* (the metric divergence that paged you), the *kill switch* (rehearsed rollback in minutes), the *triage* (which stack layer failed), the *comms template*, and the *eval write-back*. "I communicated and reassured" leaves the interviewer doubting you've run one.

---

## The go/no-go table — every row: owner + binary criterion

| Gate | Owner | Pass criterion |
|---|---|---|
| Context-of-use memo (MAP) | PM | user / task / failure-mode named |
| Distributional done + baseline beat | PM | ≥90% grounded+cited; tail escalates; beats FAQ baseline |
| Eval suite green (MEASURE) | PM + Eng | regression + frontier pass; judge calibrated to golden set |
| Risk Report published | PM | categories × levels × mitigations × residual × rollback |
| Guardrails active | Eng | ≥2 rails/path; scoped perms + confirm + caps; kill switch **tested** |
| Rollback rehearsed | SRE + PM | staging revert measured **< 15 min** |
| Monitoring live day one | SRE | quality / cost / latency / **drift** + alerts |
| Release plan signed (MANAGE) | PM + Rel Mgr | all rows met or residual accepted |

*"The responsible-AI team will review"* is not a gate.

---

## 📚 Resources

*Typed, annotated, capped. `📄 paper · 📘 docs · 🎬 video · 📰 article · 💻 repo`.*

- 📘 [Demystifying evals for AI agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents) — Anthropic Engineering. Eval-driven development, `pass@k`, eval saturation, balanced sets. *License: Anthropic docs — link + commentary.*
- 📰 [LLM Evals: Everything You Need to Know](https://hamel.dev/blog/posts/evals-faq/) — Hamel Husain, Jan 2026. Error-analysis-first, the ~80/15/5 modality mix, integrity rules.
- 📰 [AI Evals for Product Managers](https://www.productboard.com/blog/ai-evals-for-product-managers/) — Productboard, Nov 2025. The 4-part rubric (Role/Context/Goal/Labels) + 4 methods.
- 📰 [Evals for PMs](https://www.braintrust.dev/blog/evals-for-pms) — Braintrust, Mar 2026. The four monitoring families incl. drift.
- 💻 [Language Model Evaluation Harness](https://github.com/EleutherAI/lm-evaluation-harness) — EleutherAI. The de-facto offline eval harness. *License: MIT · 13.1k★.*
- 💻 [Ragas](https://docs.ragas.io/en/stable/) — Vibrant Labs. RAG-specific eval (faithfulness, context recall). *License: Apache-2.0.*
- 📘 [NIST AI Risk Management Framework 1.0](https://www.nist.gov/itl/ai-risk-management-framework) — NIST. GOVERN/MAP/MEASURE/MANAGE — the shape, not the thresholds. *License: US-gov public.*
- 📄 [Responsible AI Checklist](https://trustarc.com/wp-content/uploads/2024/05/Responsible-AI-Checklist-.pdf) — TrustArc, 2024. A concrete pre-launch checklist to adapt.

---

**Next:** [06 · Model & Vendor Selection](06-model-and-vendor-selection.md) · Back to the [interview-loop map](../README.md#the-ai-pm-interview-loop-map) · Drill the [AI-specific cases bank](../questions/ai-specific-cases.md)
