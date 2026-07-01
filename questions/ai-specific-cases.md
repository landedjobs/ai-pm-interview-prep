# AI-Specific Cases — Question Bank

*30 questions on the verbatim 2026 prompts: "design an AI feature for X", "how would you measure it", "how do you handle hallucinations", the eval harness, and safe launch. Checkpoints (✅ correct; every option carries a why) + real-loop questions with provenance. Updated 2026-07.*

Legend: ✅ correct · ✅ **Reported** · 🔮 **Representative** · 🟢🟡🔴 difficulty.

Study first: [content/05 · Evaluation & Responsible-AI Launch](../content/05-evaluation-and-responsible-ai-launch.md) · [content/07 · Human-AI Interaction](../content/07-designing-human-ai-interaction.md) · [worked answers](../answers/).

---

## Design an AI feature (scoping & architecture)

**Q1 🟡 — A stakeholder pitches "a smart assistant in our app that can help with anything." Using the scoping framework, what do you push back with first?**

- ▫️ Great — ship a chat box and let users discover what it can do — *A bare "anything" chat box is broad on every NN/g lever; success is indistinguishable from hallucination.*
- ✅ Narrow it: define accepted inputs, possible outputs, in-scope tasks, and covered subjects for one concrete job — if any answer is "anything," it isn't scoped — *Scoping the four levers turns an untestable surface into a gradable feature.*
- ▫️ Add a bigger model so it can truly handle anything — *Model size doesn't make an unbounded feature predictable.*

**Q2 🟡 — For a support product, which feature is the legitimate agentic candidate, and how should it be bounded?**

- ▫️ The brand-voice macro rewrite — make it an autonomous agent — *A high-volume, fixed, single-step rewrite is a fine-tune/distill case, not an agent.*
- ✅ The order-status resolver — a tightly scoped agent over read-only MCP tools, with any refund action gated behind a human and a step cap — *Multi-tool, mostly read-only lookups are the agentic fit; gate the irreversible refund.*
- ▫️ The ticket auto-reply — make it a fully autonomous agent that sends replies — *Auto-reply is a RAG + structured-output draft task with a human approving the send.*

**Q3 🔴 — Choosing the architecture for a shopping agent. Best-reasoned answer?**

- ▫️ A multi-agent system (planner, searcher, negotiator, buyer) for max capability — *Inverts Anthropic's rubric; unnecessary orchestration adds failure surface.*
- ✅ Start with a single-purpose recommend-and-compare tool grounded in catalog data; add multi-step agency only when the eval shows it fails on genuinely multi-step tasks — *Start simple, add agency only when simpler solutions fail; name the failure mode that justifies escalation.*
- ▫️ Whatever framework is most popular this quarter — *Not an architecture rationale.*

**Q4 🟡 — Define the MVP for an AI feature whose model quality will improve over time. Strongest definition?**

- ▫️ The smallest set of features we can ship quickly — *The traditional MVP framing; misses that AI MVPs are about measuring and improving quality.*
- ▫️ Whatever the demo already does well — *Bakes in cherry-picked performance and gives no measurable quality surface.*
- ✅ The smallest workload where we can measure model quality — shipped with an eval and room for the quality curve to climb as data accrues — *AI MVPs metabolize data into quality over time.*

**Q5 🟡 — About to commit engineering to a RAG ticket auto-reply. The disciplined validation sequence?**

- ✅ Wizard-of-Oz/mock for the flow, then a golden eval set + red-team pass, then a shadow/canary on 1–5% of traffic with a kill switch — each with a go/no-go — *The three-prototype rule closes the demo-to-production gap.*
- ▫️ Ship to 100% of users and watch the support queue — *Skips the golden-set and shadow stages, exposing the long tail to everyone at once.*
- ▫️ Demo it to leadership; if the demo is clean, build and launch — *A clean demo is survivorship bias on cherry-picked inputs.*

---

## How would you measure it / define done

**Q6 🟡 — In a case round, deflection (your NSM) is up 9% and leadership wants to declare success. What does a senior AI PM say?**

- ▫️ Confirm the 9% is statistically significant, then declare success — *Significance confirms the product metric moved; not whether the model is silently degrading on a slice.*
- ▫️ Declare success — a rising NSM is the definition of a working feature — *A rising NSM can coexist with a rotting model on a cohort.*
- ✅ Not yet — check whether model quality diverged from the rising NSM via a held-out, per-slice faithfulness gate that runs independently of deflection — *Only an independent, per-slice, held-out quality gate surfaces the silent regression.*

**Q7 🟡 — Building the eval suite for a support assistant, a teammate says "let's collect 20,000 labelled tickets and score accuracy on all of them." Better design?**

- ▫️ Agree — the larger the labelled set, the more trustworthy the eval — *Volume isn't the bottleneck; coverage is. A single accuracy number hides stratified failure.*
- ✅ A portfolio: ~100 purpose-built regression examples (deterministic assertions) + 100+ sampled prod traces every 2–4 weeks (LLM-judge) + ~20 frontier cases, covered by dimension, with retrieval scored before generation — *Three sets across three modalities; coverage by dimension, gold contexts from the production retriever, recall@k before faithfulness.*
- ▫️ Skip offline evals and rely on production thumbs-up at scale — *Thumbs-up is biased toward confident-but-wrong answers and offers no pre-release gate.*

**Q8 🟡 — "The model is 85% accurate — do we ship?" in the support-assistant case. Strongest first move?**

- ✅ Refuse the binary and write a distributional done: a target rate on a scoped input class, a graceful tail (escalate, don't guess), a hard floor on unsupported claims, and a baseline the AI must beat — *The universal case-round opener.*
- ▫️ Ask which model and provider, then pick the highest-accuracy option — *Downstream, and accuracy alone is still a flat number.*
- ▫️ Say yes — 85% clears the bar for a support bot — *No scoped class, no tail, no floor, no baseline is the weak answer.*

---

## How do you handle hallucinations

**Q9 🟡 — A feature must answer strictly from help-center articles and abstain when the answer isn't there, but the prompt has no documents. Why does it still hallucinate, and the fix?**

- ▫️ The instructions aren't firm enough — add "only answer from official policy" more forcefully — *Instructions can't supply facts the model never received.*
- ✅ There's nothing to ground on — retrieve the relevant articles into the prompt and explicitly allow "I don't know," then evaluate groundedness — *A grounding gap: put the source text in context (RAG) and permit abstention, then measure.*
- ▫️ Increase the number of few-shot examples — *Examples teach shape and tone, not your specific facts.*

**Q10 🔴 — Your customer-facing support bot occasionally states a refund policy that doesn't exist. Beyond fixing the prompt, the launch-gate-level concern?**

- ▫️ None — occasional wrong answers are inherent to LLMs — *Air Canada lost in tribunal on exactly this; an ungrounded confident misstatement is legally-actionable speech the business owns.*
- ✅ The bot's confident misstatements are legally attributable to the company (Air Canada) — gate on retrieval grounding, a confidence-threshold human escalation, and citation/policy-existence eval checks — *Moffatt v. Air Canada makes ungrounded output a liability.*
- ▫️ Switch to a larger model so it hallucinates less — *A bigger model reduces but doesn't eliminate ungrounded claims and adds no grounding or escalation.*

**Q11 🔴 — The agent recommended a product that doesn't exist. Which PAIR bucket, and the fix?**

- ▫️ User error — the user asked badly — *The input was reasonable.*
- ✅ System error — ground in tool/catalog results, abstain on no-match, and add a fake-SKU output guardrail — *The model invented a product; ground the answer in retrieved facts and refuse when there's no match.*
- ▫️ Context error — the world changed — *Nothing about the world changed; the model confabulated a product that was never real.*

**Q12 🔴 — On-call gets one screenshot of a wrong AI answer from a single user. How should the incident process treat it?**

- ▫️ Immediately roll back the feature — any wrong output is a SEV-1 — *Over-reaction: a probabilistic product produces individual misses by design.*
- ✅ Log it as a sample and check the failure-rate trend; escalate only if the rate has shifted (e.g. hallucination rate up >10pp WoW), and fold the case into the eval set — *AI incidents are defined by a shift in the failure rate, not a single output.*
- ▫️ Ignore it — single outputs never matter — *Under-reaction: the sample is valuable evidence to log and fold into the eval set.*

---

## The eval harness (offline + online)

**Q13 🟡 — Where should an AI PM spend the most human-labelling effort?**

- ✅ Building and maintaining a high-quality golden dataset that defines the rubric and calibrates the LLM judge — *Humans are best spent on ground truth, not grading every trace.*
- ▫️ Manually scoring as many production traces as possible each week — *The unscalable human-only pattern.*
- ▫️ Re-labelling the offline regression set every build — *A regression set should be stable.*

**Q14 🔴 — You're standing up the first eval set for an AI assistant. A teammate wants to wait until you've collected 50,000 labelled examples. Better move?**

- ▫️ Agree — more data always means a more trustworthy eval — *Coverage, not volume, is the bottleneck.*
- ▫️ Wait, but only for the regression set — *Still over-indexes on volume and stalls the program.*
- ✅ Start with 10–20 examples per scenario covering distinct dimensions (incl. negative/"should-refuse" cases), then grow weekly from production traces — *Coverage by dimension beats raw row count and unblocks you immediately.*

**Q15 🟡 — Your eval mix instruments quality, cost, and p95 latency after launch. A reviewer says it's still incomplete for an AI product. What's missing?**

- ✅ Drift — semantic/embedding/label drift detection, which catches the input distribution shifting under a model that still looks healthy per request — *The most-often-missing leg; without it you don't see distribution shift until churn appears.*
- ▫️ Nothing — quality, cost, and latency fully cover an AI product — *They omit drift.*
- ▫️ Total request count — *Table-stakes telemetry, not the missing AI-specific family.*

---

## Responsible-AI & safe launch

**Q16 🟡 — A PM says "we'll comply with the NIST AI RMF, so we're covered on go/no-go thresholds." The issue?**

- ▫️ NIST RMF is outdated and superseded by the EU AI Act — *Neither outdated nor a substitute relationship.*
- ✅ NIST provides the shape (GOVERN/MAP/MEASURE/MANAGE) but no quantitative thresholds — the concrete go/no-go numbers must come from your own policy, linked to verifiable artifacts — *NIST deliberately gives structure, not numbers.*
- ▫️ NIST RMF only applies to government systems — *It's cross-industry guidance.*

**Q17 🔴 — Your team ships an output content-moderation filter and calls the launch "safe." A security reviewer pushes back. Why?**

- ✅ One output filter is a single rail — it can't stop prompt injection on the input, poisoned retrieval, or unsafe tool calls; you need defense in depth (≥2 rails per path) and a severity-tiered response — *No single mechanism suffices; input+output (plus dialog/retrieval) and a severity map are required.*
- ▫️ Output filters add too much latency — *Latency isn't the objection.*
- ▫️ Content moderation should be done by humans — *Automated moderation is fine as one rail; the issue is relying on it alone.*

**Q18 🔴 — Design guardrails for an agent that can send emails and make purchases on a user's behalf. Which answer is strongest?**

- ▫️ Add safety filters and monitor for misuse — *Vague and rail-free — no scoping, no confirmation, no limits, no kill switch.*
- ✅ Least-privilege scoped permissions per action, human confirmation for irreversible actions, spending caps and per-session rate limits with numbers, and a documented kill switch + on-call rotation — *The full execution-rail design.*
- ▫️ Only allow the agent to take "safe" actions — *"Safe actions" is undefined hand-waving.*

**Q19 🟡 — A PM reports "we red-teamed the model for safety before launch." What does a senior reviewer ask to know if that's meaningful?**

- ▫️ How many hours the red team spent — *Tester-hours don't determine coverage.*
- ▫️ How many people were on the red team — *Headcount isn't the coverage lever.*
- ✅ Which public harm taxonomy was used, which methods (human + automated), what was covered AND not covered, and whether findings became permanent eval cases — *Coverage = taxonomy breadth × methodology diversity × write-back.*

**Q20 🟡 — Across Anthropic, Microsoft, Meta, and OpenAI, what single pre-release artifact do their responsible-AI processes converge on?**

- ▫️ A public leaderboard score — *A leaderboard number doesn't name risks, mitigations, or residual uncertainty.*
- ✅ A Risk Report naming categories, risk levels, mitigations, and residual uncertainty (plus rollback), attached to the release — *RSP thresholds, Microsoft's Responsible Release Plan, Meta's Llama assessments, and OpenAI's Preparedness Framework all converge here.*
- ▫️ A marketing launch blog post — *Not a governance artifact.*

**Q21 🟡 — About to roll out a new model that improves offline scores, with low confidence about real-world behaviour and a need for zero customer risk. First step?**

- ✅ Shadow test — run the new model on real traffic but discard its outputs, comparing against production with no customer exposure — *The lowest-risk default: see the real distribution with zero user impact.*
- ▫️ Run a 50/50 A/B immediately for the fastest read — *A/B exposes half your users to an unvalidated model.*
- ▫️ Ship to 100% and watch the dashboards — *The "ship ahead of evaluation" pattern that caused Google's Gemini incident.*

**Q22 🔴 — A launch checklist row reads "Rollback: documented in the runbook." Why does a senior PM mark this gate not-passed?**

- ▫️ Runbooks are obsolete — rollback should be fully automated — *The specific gap is that the rollback has never been executed.*
- ✅ A documented-but-unrehearsed kill switch is a hypothesis, not a kill switch — require a staging rollback drill with a measured time-to-revert (target <15 min) — *"Written down" is not "executed under pressure."*
- ▫️ The runbook should be owned by the PM, not engineering — *Ownership aside, the failing criterion is the missing rehearsal.*

---

## The capstone loop

**Q23 🟡 — Outlining the agentic personal-shopper PRD with 30 minutes. Where do you spend the most tokens to signal senior judgment?**

- ▫️ A thorough problem statement and market sizing — *The strategic-foundation floor.*
- ✅ The AI-specific cluster — eval framework, numeric quality bar, guardrails, and the human-confirmed action gate — *Graded on evaluation rigor and risk/trust.*
- ▫️ A polished GTM and pricing plan — *One section of the operational plan.*

**Q24 🟡 — For v1 of the agent, where do you place the human-in-the-loop, and why?**

- ✅ At the irreversible step — the agent drafts the cart, a human confirms before any purchase is charged — *The action gate bounds the catastrophic tail.*
- ▫️ Nowhere — full autonomy is the point — *Mis-prices the cost-of-error tail.*
- ▫️ At every step, approving each recommendation — *Destroys the product value.*

**Q25 🔴 — "Three weeks after launch, recommendation quality dropped 15% and you shipped nothing." Most likely cause + safeguard?**

- ▫️ Random variation — nothing to do — *A sustained drop with no deploy isn't noise.*
- ▫️ The user base changed; wait — *Doesn't explain a broad sustained drop, and "wait" isn't a safeguard.*
- ✅ The provider silently updated the model — pin model + prompt versions and gate every change on an eval — *Pinned versions + a gold-set gate catch provider-side changes before users do.*

**Q26 🔴 — Your launched feature works well for 90% of users but poorly for a specific segment. Senior response?**

- ▫️ Collect more data and retrain — it'll average out — *Vague "more data" risks re-baking the same skew.*
- ✅ Run a subgroup audit to localize the failure, then augment/reweight the data or route that segment to a stronger path before re-launch — and track the segment metric — *Diagnose the 10% specifically, fix with targeted data/routing, instrument the subgroup metric.*
- ▫️ Ship as-is; 90% is good enough — *Ignores the trust and fairness cost.*

---

## Real-loop AI-specific cases

> **✅ Reported** unless labeled 🔮. These map directly to worked answers in [/answers](../answers/).

**Q27 ✅ Reported 🔴 — "How would you detect and mitigate hallucinations of an LLM feature in production?"** — [KORE1 2026](https://www.kore1.com/ai-product-manager-interview-questions-2026/). Detect: grounded-faithfulness eval on sampled traffic + a per-slice divergence alarm + a citation/existence check. Mitigate: RAG grounding, enforced abstention, confidence-threshold escalation, and write-back of incidents. Full answer: [answers/04](../answers/04-hallucination-in-production.md).

**Q28 ✅ Reported 🔴 — "Lead an AI launch decision when the eval shows only 60% pass on critical safety checks."** — [KORE1 2026](https://www.kore1.com/ai-product-manager-interview-questions-2026/). Do not ship; a 60% safety pass is a hard-floor failure. Scope down to the covered slice, shadow-serve the rest, set the safety floor as a go/no-go gate, and re-red-team. Full answer: [answers/05](../answers/05-launch-gate-60pct-safety.md).

**Q29 ✅ Reported 🟡 — "Design an AI feature for [X] and describe its success metrics, eval set, and guardrails."** — pattern across [Meta's Product Sense with AI](https://www.lennysnewsletter.com/p/building-ai-product-sense-part-2) and [KORE1](https://www.kore1.com/ai-product-manager-interview-questions-2026/). Run the [six lenses](../frameworks/README.md#2-the-six-lenses--ai-product-sense) → the [AI-feature-design template](../frameworks/README.md#7-the-ai-feature-design-template). Worked: [answers/01](../answers/01-ai-feature-clinic-messaging.md), [answers/02](../answers/02-ai-feature-recruiting-screener.md).

**Q30 ✅ Reported 🔴 — "Walk me through your eval harness — offline eval set + online measurement."** — [KORE1 2026](https://www.kore1.com/ai-product-manager-interview-questions-2026/). Offline golden/regression (precision/recall/faithfulness) + online task-completion/escalation/correction-rate on sampled traffic + human calibration, gated by a ship threshold. Worked: [answers/03](../answers/03-eval-harness-support-assistant.md).

---

*More: [product-sense](product-sense.md) · [technical-fluency](technical-fluency.md) · [metrics](metrics.md) · [behavioral](behavioral.md) · [index](README.md). See the [worked answers](../answers/) for full case walkthroughs.*
