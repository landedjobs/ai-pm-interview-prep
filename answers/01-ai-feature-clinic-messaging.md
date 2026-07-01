# Worked Answer 01 — AI assistant that drafts replies to patient messages

**Prompt (Product Sense with AI round):** *"Design an AI assistant that drafts replies to patient messages for a clinic."*

> [!NOTE]
> This is a **high-stakes, regulated** case. The single fastest way to pass it is to name the cost-of-error tail *first* and let every later decision follow. The single fastest way to fail it is to jump to "we'll build a chatbot that answers patient questions."

---

## 0. Scope first (30 seconds, out loud)

- **User & job?** Clinic *staff* (nurses/front-desk), not patients directly — the job is to *draft* a reply a clinician reviews, cutting the 4–6 min they spend per message.
- **Autonomy?** Draft-only in v1. The assistant never sends unsupervised.
- **Stakes?** A wrong medical statement is **dangerous and regulated** (HIPAA + clinical liability), not merely annoying.
- **Architecture?** The model reasons; deterministic tools fetch the chart and the clinic's approved protocols.

---

## 1. The cost-of-error tail (Trust & Liability lens — lead here)

A wrong reply about medication, dosage, or symptoms is a **catastrophic, irreversible, regulated** failure — the Air Canada shape with a clinical multiplier. So this feature is scoped as **assistive with a hard human approval gate**, and the eval rigor, refusal behavior, and monitoring all inherit from that one call. *I would not build an autonomous patient-facing responder at any accuracy number.*

## 2. The six lenses

| Lens | Call |
|---|---|
| **User Reality** | clinic staff drowning in an inbox; the job is triage + a first-draft reply, high frequency, high severity |
| **Model Behavior** | reliable at *drafting from provided context*; **not** reliable at unsupervised clinical judgment → tier 3, gate it |
| **Economics** | route a cheap model for triage/classification, a stronger model only for the draft; bind cost to $/reviewed-message |
| **System Design** | the LLM is one stochastic step — deterministic tools pull the patient's chart + approved protocol docs (RAG), the model drafts, a validator checks for out-of-scope claims |
| **Trust & Liability** | the tail is a wrong clinical statement → **draft-only, human confirms, refuse outside a safe scope** |
| **Go-to-Market** | launch to one clinic's staff as a supervised beta (a verifiable-tolerance segment), never patient-facing solo advice |

## 3. The wedge

Not "an AI that answers patient questions." The wedge: **an assistant that drafts every reply grounded in the patient's own chart and the clinic's approved protocols, with a clinician confirming before send.** Trust and fit are the moat — grounding + the approval gate — not raw model quality.

## 4. Failure modes → recovery (PAIR)

- **Input error** (vague message — "I feel off") → the assistant asks a structured clarifying question rather than guessing.
- **System error** (model drafts an unsupported clinical claim) → ground in retrieved protocols; **abstain and escalate** ("I can't safely draft this — routing to a clinician") when no protocol covers the case.
- **Context error** (a protocol was updated) → re-fetch protocols at draft time; show a "last verified" stamp on the source.

## 5. Architecture (lowest agency that works)

A **RAG + structured-output** draft — *not* an agent. Input = the patient message + chart; output = `{draft_reply, cited_protocol_ids, confidence, escalate?}`; the reply fails closed to "escalate" when confidence is low or no protocol matches. Cheap model on triage, frontier on the draft.

## 6. Success metrics — the contract

```
Business:   25% reduction in clinician minutes-per-message within 90 days (named persona: nurses).
Model bar:  >=95% of drafts grounded + cited; <1% unsupported-clinical-claim rate;
            100% refusal on out-of-scope (dosage changes, diagnosis) that must route to a clinician.
Guardrails: input PII/PHI handling per HIPAA; output = no unsupported claim, no dosage advice.
Operational: p95 draft latency <3s; cost/reviewed-message <$0.05.
Divergence alarm: weekly per-condition faithfulness gate, independent of the minutes-saved metric.
```

One input (edit-after-draft rate), one output (clinician minutes saved), one guardrail (unsupported-claim rate as a kill-switch).

## 7. The launch gate

Shadow the drafts against real messages (clinicians see them but the eval discards them) → canary to a few nurses → gate expansion on the **lower CI bound** of the faithfulness metric, not the point estimate. Rehearsed rollback (<15 min). Day-one monitoring on quality/cost/latency/**drift** (new symptom patterns). Every escalation and every corrected draft becomes a permanent eval case.

---

## What a follow-up will push on

- *"What accuracy do you need to make it patient-facing?"* → **None.** The constraint is the irreversible clinical tail, not raw capability; patient-facing autonomy stays out of scope until there's a regulatory and eval story that doesn't exist yet.
- *"How do you know it's working on a rare condition?"* → per-condition slice in the eval + a divergence alarm, not an aggregate faithfulness number.
- *"Cut v1 in half."* → drop the multi-condition coverage; ship one high-volume, low-risk category (appointment logistics) first, prove the loop, then expand.

**Framework applied:** [Six Lenses](../frameworks/README.md#2-the-six-lenses--ai-product-sense) · [PAIR failure taxonomy](../frameworks/README.md#9-the-pair-failure-taxonomy) · [AI-feature-design template](../frameworks/README.md#7-the-ai-feature-design-template).

*[← Answers index](README.md) · [Next: recruiting screener →](02-ai-feature-recruiting-screener.md)*
