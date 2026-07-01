# ✍️ Worked Answers

*Original, senior-grade walkthroughs of the AI-specific case prompts — each built against a [framework](../frameworks/README.md), each carrying the rubric signal: **a specific metric, a specific failure mode, and a specific user.** Updated 2026-07.*

> [!NOTE]
> These are **models, not scripts.** In a real round you'd say a third of this out loud and defend it under follow-ups. Read them for the *shape and order* of a senior answer — and notice how every one leads with the cost-of-error tail before the UX, and closes on an eval + a gate.

| # | Prompt | Framework(s) applied |
|---|---|---|
| [01](01-ai-feature-clinic-messaging.md) | Design an AI assistant that drafts replies to patient messages for a clinic | Six Lenses · PAIR taxonomy · action gate |
| [02](02-ai-feature-recruiting-screener.md) | Design an AI resume-screener — and keep it fair | Six Lenses · fairness/subgroup audit · trust design |
| [03](03-eval-harness-support-assistant.md) | "Walk me through your eval harness" for a support assistant | Eval-suite portfolio · modality mix · calibrated judge |
| [04](04-hallucination-in-production.md) | How would you detect and mitigate hallucinations in production? | Grounding · abstention · divergence alarm · incident loop |
| [05](05-launch-gate-60pct-safety.md) | Lead a launch decision when the eval shows 60% pass on safety | Go/no-go table · NIST RMF · defense-in-depth |
| [06](06-prd-agentic-shopping-assistant.md) | Outline a PRD for an agentic personal-shopping assistant | 12-section PRD · action gate · Anthropic agent patterns |
| [07](07-debug-ladder-quality-regression.md) | "Quality dropped 15% three weeks post-launch — debug it" | The debug ladder · pinned versions · eval gate |

---

## The universal answer shape

Every answer here follows the same senior arc — steal it:

1. **Scope first.** Ask the clarifying questions (autonomy? stakes? architecture? economics?) before designing. An "agent" hides a dozen systems.
2. **Name the cost-of-error tail** *before* the UX — reversible / expensive / irreversible / regulated. This sets eval rigor, HITL, and refusal behavior.
3. **Pick the wedge** — the defensible *application* of a commodity capability, plus the segment that tolerates probabilistic quality.
4. **Classify the failure modes** (input / system / context) and design the recovery *as a feature*.
5. **Choose the architecture** at the lowest agency that works (prompt → RAG → fine-tune → agent), gated by an eval.
6. **Make the quality bar a contract** — one input metric, one output metric, one guardrail; numeric thresholds, not adjectives; a golden set + a cadence.
7. **Gate the launch** — shadow → canary rollout on a CI lower bound, a rehearsed rollback, day-one quality/cost/latency/drift monitoring, incident write-back.

---

*Part of [ai-pm-interview-prep](../README.md). Drill the matching [questions](../questions/README.md). Content MIT-licensed.*
