# Worked Answer 05 — Launch decision when the eval shows 60% pass on safety

**Prompt (Behavioral + Strategy + AI-ethics round, verbatim 2026):** *"Lead an AI launch decision when the eval shows only 60% pass on critical safety checks."* — [KORE1 2026](https://www.kore1.com/ai-product-manager-interview-questions-2026/)

> [!NOTE]
> This tests two literacies at once: **ethics** (can you hold a launch when the safety floor fails) and **architecture** (can you turn "not yet" into a concrete, scoped plan). The weak answer ships to a small percentage "to learn." The strong answer treats safety as a **floor, not a tradeable metric**, and produces a scoped path to a real launch.

---

## The decision: do not ship as-is

A **60% pass on *critical safety* checks is a hard-floor failure.** Safety is not a metric you trade against velocity or reach — it's a gate. Shipping even to a small percentage exposes users to critical failures at that rate, and reach *multiplies* the tail. So the headline answer is: **hold the general launch.** But "no" is not the whole answer — the senior move is to convert it into a scoped plan.

## Step 1 — Decompose the 60%

"60% pass" is a blended number hiding structure. First questions:

- **Which checks are failing?** Stratify by harm category (prompt-injection/exfiltration, PII leakage, toxic output, unsafe tool calls). A 60% that's uniform is different from a 60% concentrated in one Critical category.
- **What severity?** Map each failing category to a Bug-Bar severity. Critical content-safety failures (e.g. injection-with-exfiltration) are launch-blocking regardless of the average.
- **Is the eval itself trustworthy?** Is coverage adequate, is the judge calibrated, are the "should-refuse" negative cases present? A green-looking 40% could be worse than a measured 60%.

## Step 2 — Scope down to what *does* clear the floor

If a subset of use cases / input classes clears the safety bar cleanly:

- **Ship the covered slice** (narrow inputs/outputs/tasks/subjects) where safety passes.
- **Shadow-serve or human-review** the uncovered slice — run the model on it with outputs discarded (or gated behind a human) to collect data at zero user risk.
- **Set the safety floor as an explicit go/no-go gate** in the launch table — a named owner and a binary criterion, not "the safety team will review."

## Step 3 — Defense in depth for what ships

One filter isn't safety. Ship the covered slice with **≥2 rails per path**: input rails (prompt-injection, PII detection) *and* output rails (toxicity, unsafe-action blocking), plus a severity-tiered incident response. For any action-taking, add least-privilege scoped permissions, human confirmation on irreversible actions, spend/rate caps, and a **tested** kill switch.

## Step 4 — The path back to a full launch

- **Re-red-team** with a named public harm taxonomy, human + automated methods, and explicit coverage of what's *not* tested.
- **Fold every failing case into the eval set** as a permanent regression test (write-back), so the 60% becomes a rising, tracked number.
- **Publish a Risk Report** (categories × levels × mitigations × residual uncertainty × rollback) attached to the scoped release — the artifact every frontier lab converges on.
- **Re-gate** the general launch on the safety floor crossing the threshold on the *lower CI bound*, not the point estimate.

## Step 5 — Communicate it up

Frame it as a **falsifiable hypothesis, not a delay**: "We ship the [covered slice] now behind two rails; we hold [uncovered slice] until safety-check pass rate on that slice clears [X]% with a rehearsed rollback. Owner: [name]. Re-evaluation: [date]." Executives fund a bounded, evidenced plan; they don't fund "we'll be careful."

---

## The one-paragraph answer

> *"I hold the general launch — a 60% pass on *critical safety* is a hard-floor failure, not a tradeable metric. Then I decompose it by harm category and severity, ship only the slice that clears the floor behind ≥2 rails, shadow-serve the rest, set the safety floor as an owned go/no-go gate, re-red-team with a named taxonomy, fold every failure back into the eval, publish a Risk Report, and re-gate the full launch on the lower CI bound crossing the threshold."*

## Follow-up pushes

- *"Leadership wants it out this quarter."* → the scoped slice can ship this quarter; the uncovered slice ships when the safety floor clears — I put the number and the owner on it.
- *"Isn't 60% good enough for a beta?"* → not for *critical safety* — a beta label calibrates *quality* expectations, not *safety* liability (Recall is the cautionary case).

**Framework applied:** [the go/no-go table + NIST RMF](../frameworks/README.md#10-nist-ai-rmf--responsible-launch) · [content/05 §responsible-AI + rollout](../content/05-evaluation-and-responsible-ai-launch.md#4-responsible-ai-gates--build-it-in-dont-audit-for-it).

*[← Previous](04-hallucination-in-production.md) · [Answers index](README.md) · [Next: agentic PRD →](06-prd-agentic-shopping-assistant.md)*
