# Behavioral & Strategy — Question Bank

*18 questions on AI failure stories, stakeholder tension, and responsible-AI judgment — the largest-weight round (~35%) that most candidates skip. Checkpoints (✅ correct; every option carries a why) + real-loop questions with provenance. Updated 2026-07.*

Legend: ✅ correct · ✅ **Reported** · 🔮 **Representative** · 🟢🟡🔴 difficulty.

> [!IMPORTANT]
> **Behavioral is ~35% of the AI-PM round** (Aakash Gupta, 47 placements) — the single largest category, and the one candidates over-prep product sense at the expense of. **Prepare 2–3 STAR-format AI failure stories before anything else.** The three laws for AI behavioral answers: **(1) hallucinations happen** (acknowledge model limits), **(2) bias surfaces** (name a fairness/segment risk), **(3) failure stories must be specific** (metric that broke + missing guardrail + fix shipped).

Study first: [content/02 §Exec comms](../content/02-ai-product-sense.md) · [content/05 §Incident loop](../content/05-evaluation-and-responsible-ai-launch.md#5-rollout-rollback-and-the-incident-loop).

---

## AI failure stories & ownership

**Q1 🔴 — "Tell me about an AI product decision you got wrong." Highest-scoring answer per Aakash's three laws?**

- ✅ "We shipped a summarizer with no hallucination guardrail; faithfulness dropped to ~80% on long docs, support tickets spiked 12%, so I added a grounding check + abstention and a faithfulness eval gate before the next release." — *Specific metric that broke + the missing guardrail + the structured fix shipped, with ownership.*
- ▫️ "We had to roll back a feature once because it wasn't working well." — *Vague rollback stories lose the largest-weight round.*
- ▫️ "The model just wasn't good enough yet, so we waited for a better one." — *Deflects ownership; the strong answer names what the PM failed to do.*

**Q2 🔴 — Behavioral: "Walk me through handling an AI incident." What separates a strong answer?**

- ▫️ "I escalated quickly and reassured the affected customers." — *Speed-and-empathy shows no system-design literacy — the weak answer.*
- ✅ Name the detector (the metric divergence that paged you) → triage (which stack layer: drift / prompt / retrieval / model swap) → mitigate (disable a rail) → comms template → rollback (rehearsed drill) → eval write-back — *AI incidents are graded on system-design literacy, not empathy.*
- ▫️ "I rolled back the whole feature immediately to be safe." — *Over-reaction on a single sample; incidents are defined by a rate shift, not one output.*

**Q3 🟡 — "Tell me about a time you communicated AI limitations to stakeholders." Strongest shape?**

- ✅ A specific limit-setting conversation: the number you put on the uncertainty ("~8 of 100 tickets still need a human") and the decision it unblocked — *Translate a distribution into a decision, with a concrete number.*
- ▫️ "I explained that AI isn't perfect and set expectations." — *Vague; no number, no decision unblocked.*
- ▫️ "I told them we'd need more time to make it reliable." — *Deflects with a timeline instead of quantifying the uncertainty.*

---

## Exec comms & narrative

**Q4 🟡 — 10 minutes with the exec team to pitch an AI support co-pilot. Best opening?**

- ▫️ Start with the architecture: the RAG pipeline, the model, the eval harness — *The canonical way to lose an exec room; executives fund outcomes, not pipelines.*
- ✅ Open with a named customer and their workflow, quantify the problem in hours/dollars, then present the bet as a hypothesis with thresholds and ask for fund/kill/study — *The briefing arc that survives a skeptical CFO.*
- ▫️ Lead with how this keeps the company competitive against AI-native rivals — *Vague and unfalsifiable.*

**Q5 🔴 — "Why does storytelling matter MORE in the AI era, when AI can write the deck for you?"**

- ▫️ It doesn't — AI writing the deck means comms matters less — *Inverts Gothelf's thesis.*
- ▫️ Polished slides always win deals — *Polish without substance is "storytelling as theatre."*
- ✅ AI commoditizes the surface (the deck), so the differentiator becomes the evidence the room can grip — the eval set, labeled examples, and named user that prove real expertise — *Gothelf's exact argument.*

**Q6 🟡 — Your AI PRD is headed to the C-suite. Per Korn Ferry, which three objections should it pre-empt?**

- ▫️ "Is it on brand / on time / on budget?" — *Delivery-management concerns.*
- ✅ "Is it safe?" (guardrail table), "Does it work?" (numeric bars on a gold set), "Is it worth it?" (business metric + horizon) — *Each answered by a section already in the AI PRD.*
- ▫️ "Which vendor / cloud / framework?" — *Enabler-level details.*

**Q7 🟡 — A skeptical exec asks "why did you pick this model over the cheaper one?" What artifact answers in 15 seconds?**

- ✅ A living decision log (Date / Decision / Reason / Reversible?) that records the trade-off and whether it's cheap to unwind — *Built so the "why behind each what" is scannable.*
- ▫️ The full eval report at the back of the PRD — *Not scannable in 15 seconds.*
- ▫️ A verbal explanation from memory — *Slower, less consistent, not auditable.*

**Q8 🟡 — An exec asks you to translate "92% eval accuracy" for a non-technical room. Best move?**

- ▫️ Quote the 92% and explain what "eval accuracy" means — *Executives can't act on a probabilistic metric they don't reason in.*
- ✅ Reframe as a rate the business understands: "8 of 100 tickets still need a human — today it's 30, so ~73% deflection," and bound the tail ("in the worst 2% it declines and routes to a person") — *Move every probabilistic fact into the deterministic frame execs act in: dollars, headcount, reversibility.*
- ▫️ Round it up to "basically 95%+ reliable" — *Overstates and still doesn't bound the tail executives actually fear.*

---

## Responsible-AI judgment

**Q9 🔴 — "Your model works for 90% of users but poorly for one demographic — what do you do, and on what timeline?" Strong shape?**

- ✅ Segment the harm (stratify the eval across the slice), quantify it (what/how-often/severity), ship the threshold not the apology ("we don't auto-launch for group X until faithfulness on that slice hits Y; until then shadow-serve/human-review"), and name the remediation owner + deadline — *Names a taxonomy, a threshold, a slice, a gate, and an owner.*
- ▫️ "We'd test for bias and check edge cases." — *The generic platitude — no taxonomy, threshold, slice, gate, or owner.*
- ▫️ "90% is strong; we'd ship and monitor the segment." — *Shipping a known-broken segment ignores the fairness cost.*

**Q10 🔴 — "Lead an AI launch decision when the eval shows only 60% pass on critical safety checks."**

- ✅ Do not ship — a 60% safety pass is a hard-floor failure; scope down to the covered slice, shadow-serve the rest, set the safety floor as a go/no-go gate, and re-red-team before expanding — *Safety is a floor, not a tradeable metric.*
- ▫️ Ship to a small percentage and monitor — *A 60% safety pass exposes users to critical failures even at low traffic.*
- ▫️ Ship — safety checks are conservative and real-world rates are better — *Assuming the eval overstates risk on critical safety is exactly the wrong bet.*

**Q11 🟡 — "Customers want an AI feature data science says is infeasible — how do you proceed?"**

- ✅ Reframe from feature to outcome, find the feasible slice (a narrower scope, a workflow assist, a rule-based baseline), pilot it against an eval, and let evidence — not the customer's ask or the DS "no" — set the roadmap — *Neither cave to the customer nor stop at "infeasible"; find the shippable wedge and measure it.*
- ▫️ Build what the customer asked and hope the model improves — *Ships against an infeasibility DS already flagged.*
- ▫️ Tell the customer it's impossible and move on — *Forfeits the feasible slice that might have solved 60% of the need.*

---

## Real-loop behavioral & strategy questions

> **✅ Reported** unless labeled 🔮. Answer in STAR with the three laws — and always end on a *specific metric + guardrail + fix*.

**Q12 ✅ Reported 🔴 — "Tell me about a time you shipped an AI feature that backfired."** — [Aakash Gupta (coached 47 into AI-PM roles)](https://aakashgupta.medium.com/i-coached-47-people-into-ai-pm-roles-at-300k-here-are-the-exact-questions-they-got-asked-36e239182010). The canonical AI behavioral prompt — prep this one first.

**Q13 ✅ Reported 🟡 — "An AI feature has significant technical limitations — how do you design the CX to manage expectations and trust?"** — [IGotAnOffer](https://igotanoffer.com/en/advice/ai-product-manager-interview). Calibrated trust: framing (beta), UI (citations, graded states), and a correction/escalation path.

**Q14 ✅ Reported 🟡 — "Your models produce inconsistent predictions — how do you work with scientists to address it?"** — [IGotAnOffer](https://igotanoffer.com/en/advice/ai-product-manager-interview). Non-determinism is the medium; constrain output (schema), pin versions, and gate changes on an eval — collaborate on the eval set, not vibes.

**Q15 ✅ Reported 🔴 — "Describe a time you pivoted an AI product strategy based on technical/model limitations."** — [Crosschq](https://www.crosschq.com/blog/ai-product-manager-interview-questions). Name the limitation, the eval evidence, and the narrower shippable wedge you pivoted to.

**Q16 ✅ Reported 🟡 — "How do you decide between improving model accuracy vs shipping faster?"** — [Crosschq](https://www.crosschq.com/blog/ai-product-manager-interview-questions). Ship at *eval saturation* on the slice you care about with a guardrail — Redpoint's "just do it" *assumes you have an eval*; you skip polish, not the gate.

**Q17 ✅ Reported 🔴 — "A competitor launches a similar AI feature six months before your release — what do you do?"** — [Crosschq](https://www.crosschq.com/blog/ai-product-manager-interview-questions). Defensibility is the *application wedge + data/eval moat*, not the model; re-scope to where your data/workflow is the edge.

**Q18 ✅ Reported 🟢 — "What is your pet peeve? / What makes you get up in the morning?"** — [Exponent (Microsoft AI PM)](https://www.tryexponent.com/guides/microsoft-ai-product-manager-interview). Culture-fit warm-ups — have a genuine, specific answer ready; don't over-engineer it.

Also ✅ Reported: *"Tell me about a time you built an AI product from scratch"* ([Exponent](https://www.tryexponent.com/guides/microsoft-ai-product-manager-interview)) · *"Walk me through your ideation→launch process for an AI product and what you learned"* ([Crosschq](https://www.crosschq.com/blog/ai-product-manager-interview-questions)).

---

*More: [product-sense](product-sense.md) · [technical-fluency](technical-fluency.md) · [metrics](metrics.md) · [ai-specific-cases](ai-specific-cases.md) · [index](README.md).*
