# 07 · Designing Human-AI Interaction

*The UX of probability — scoping, confidence signaling, human-in-the-loop, and the interaction patterns that turn a wrong answer into a designed moment. Updated 2026-07.*

> [!NOTE]
> **In AI, the recovery is the product.** Because output is probabilistic, the interface's job is not to hide uncertainty — it's to make the user trust the system *exactly as much as a given answer deserves*. This is design under a distribution, and it's a distinct skill the case round probes with "the model gets this wrong sometimes — what do you do?"

---

## Scope on four levers before you design anything

NN/g defines a feature's **scope** as the breadth of its data inputs and functionality, decomposed into four levers:

| Lever | Narrow → | Payoff |
|---|---|---|
| **Inputs** | guided fields, constrained formats | cuts user error |
| **Outputs** | a fixed structure (JSON, a template) | becomes gradable + renderable |
| **Tasks** | one named task | ships faster, lower error rate |
| **Subjects** | a bounded domain | raises trust, bounds the RAG corpus |

**If any lever's answer is "anything," it isn't scoped.** The failure mode to name in a review: **a chat box pasted onto your app** — broad on every lever, so the user doesn't know what to type, the model doesn't know the task, and *success is indistinguishable from hallucination.* Duolingo's AI-content rollout is the positive mirror: narrow subject (one language), narrow output (lesson templates), expert-reviewed.

---

## The calibration UI — surfacing uncertainty legibly

**Explain for understanding, not completeness** (PAIR). The pattern stack, cheapest to richest:

1. **Provenance** — show the source/citation so the user calibrates by *checking*, not trusting.
2. **Hedging language** — "based on the docs I found…" vs a flat assertion sets the prior honestly.
3. **Graded states** — a high-confidence answer, a "here's my best guess," and an "I don't know" are *visibly different* UI states, not the same chat bubble.
4. **Human handoff** — at low confidence or high stakes, route to a person rather than guess.

The anti-pattern is **a single confident-looking bubble for every answer** — it guarantees over-trust on exactly the answers that don't deserve it.

> [!WARNING]
> **Raw model confidence is not calibrated probability.** A self-reported "90% sure" doesn't mean right 90% of the time — models are over-confident. If you surface a confidence number, validate it against actual accuracy on a labeled set first. Otherwise prefer provenance and graded states, which let the user calibrate without trusting a possibly-miscalibrated number.

---

## The recovery patterns (from the PAIR failure taxonomy)

Classify the failure, then design the matching recovery:

| Failure | Recovery pattern |
|---|---|
| **User error** (bad input) | prompt repair, inline guidance, a clarifying question |
| **System error** (model missed a reasonable input) | fallback, refuse ("I don't know"), route to a stronger model |
| **Context error** (world changed) | retrieval grounding, a "last verified" freshness signal, a stale-data warning |

**Refusal is a feature.** A system that *cannot* say "I don't know" hallucinates on every out-of-corpus question. But every trust mechanism — refusal, citations, confidence thresholds, HITL — is a **coverage trade**: fewer questions answered, more trustworthily. Avoid **refusal-washing** (declining to dodge hard cases): set an explicit abstention rate and review borderline refusals.

**Design explicit feedback affordances** — thumbs up/down, *hide this recommendation*, *flag or report* — with two rules (PAIR): **acknowledge that you received the feedback**, and **tell the user how the system will respond to it.** The non-obvious payoff: thumbs-down is **RLHF-grade labeled data** ("customers are your RLHF annotators" — Redpoint). Route corrections into the eval set and gate the next model change on them.

---

## Structural interaction patterns that contain failure

- **The action gate** — everything up to "draft the cart / draft the reply / draft the itinerary" can be probabilistic and recoverable; the *irreversible* step (charge a card, send an email, book a flight) is gated by **human confirmation**. This is the single fastest thing weak candidates skip in an agentic round.
- **Tome's outline-gate** — force user agreement on an *outline before building full content*, converting an open-ended probabilistic task into an **iterative contract** the user already accepted, which contains a downstream failure (a bad outline) early.
- **Co-pilot with a human in control** (Adobe Firefly) — pair explicit signals (likes/dislikes) and implicit signals (saves/shares) to refine the model, with the user in control by construction.

> [!WARNING]
> **A polished error string is not graceful failure — it's the junior version.** Graceful failure starts by *classifying* the failure (input / system / context) and designing a *matching* recovery, then closes the loop by capturing the correction as a labeled signal. "Nice error message" treats the symptom; classification + recovery + feedback treats the cause.

---

## The cautionary case: Google Duplex

A capability that could book a restaurant convincingly but **failed the product test** because it **hid its agency**, omitted consent, and routed accountability nowhere. Capability without consent, confidence UI, and an override path is a liability. Microsoft Recall is the pre-launch version of the same mistake — a high-stakes capability (default-on screen capture) shipped without a **graduated, opt-in trust surface.**

> [!TIP]
> **Marily Nika's bar:** every AI design answer should tie to a *messy world* — hallucination, trust, and a user-override path — not a clean happy path. Name **who** your human-in-the-loop is and **at what threshold** (the confidence/stakes cutoff) they get involved.

---

## The 18 Microsoft HAX guidelines — a checklist to reference

Microsoft's Guidelines for Human-AI Interaction organize interaction design across the lifecycle:

- **Initially** — make clear what the system can do and how well.
- **During interaction** — show contextually relevant info, match social norms, mitigate bias.
- **When wrong** — support efficient invocation, dismissal, and correction; scope services when in doubt; make clear *why* the system did what it did.
- **Over time** — remember recent interactions, learn from behavior, update cautiously, encourage granular feedback, convey consequences of user actions, and notify users of changes.

Reference these by cluster in a design round — they're a shared vocabulary for "how would you handle the model being wrong / opaque / changing."

---

## 📚 Resources

*Typed, annotated, capped. `📘 docs · 📰 article`.*

- 📘 [Guidelines for Human-AI Interaction (HAX Toolkit)](https://www.microsoft.com/en-us/haxtoolkit/ai-guidelines/) — Microsoft. The 18 UX guidelines across the lifecycle. *License: Microsoft docs — link + commentary.*
- 📘 [People + AI Guidebook (PAIR)](https://pair.withgoogle.com/guidebook/) — Google. Calibrated trust, the failure taxonomy, graceful failure, feedback loops.
- 📰 [Scope in Generative AI Features](https://www.nngroup.com/articles/scope-ai-features/) — Nielsen Norman Group. The inputs/outputs/tasks/subjects levers.
- 📰 [The Shape of AI](https://www.shapeof.ai/) — a visual taxonomy of emerging AI-UX patterns.
- 📰 [AI UX Patterns](https://www.aiuxpatterns.com/) — a pattern library for confidence, citations, and correction affordances.
- 📰 [UX Best Practices for Conversational AI / Chatbots](https://www.neuronux.com/post/ux-design-for-conversational-ai-and-chatbots) — Neuron UX, 2025. Conversation-design specifics.

---

**Next:** back to the [interview-loop map](../README.md#the-ai-pm-interview-loop-map) · Work the [frameworks](../frameworks/README.md) · Drill the [AI-specific cases](../questions/ai-specific-cases.md) and see [worked answers](../answers/)
