# Product Sense — Question Bank

*35 questions on the six lenses, the wedge, segmentation, trust design, prioritization, PRDs, and exec comms. Multiple-choice checkpoints (✅ correct; every option carries a why) + real-loop questions with provenance. Updated 2026-07.*

Legend: ✅ correct · ✅ **Reported** · 🔮 **Representative** · 🟢🟡🔴 difficulty.

Study first: [content/02 · AI Product Sense](../content/02-ai-product-sense.md) · [content/03 · Writing AI PRDs](../content/03-writing-ai-prds.md) · [frameworks](../frameworks/README.md).

---

## The six lenses & the wedge

**Q1 🟡 — Design an AI assistant that drafts replies to patient messages for a clinic. Your first structural move?**

- ✅ Name the cost-of-error tail (a wrong medical reply is dangerous, not just annoying) and let eval rigor, human-in-the-loop, and refusal behavior follow from it — *The Trust & Liability lens (Aakash's "annoying vs lawsuit-prone medical reply"). Pricing the tail first forces every later decision to be coherent.*
- ▫️ Pick the highest-accuracy frontier model so quality is maximized — *Model choice is downstream; in a high-stakes domain no accuracy number removes the need for a designed recovery and a human in the loop.*
- ▫️ List the features (summarize history, draft reply, suggest tone) and prioritize them — *Feature-listing without naming the failure mode is the classic non-AI answer.*

**Q2 🔴 — "You proposed an AI study-helper for students — why wouldn't a competitor with a better model just win?" Strongest reply?**

- ▫️ We'd fine-tune on more data so our model stays ahead — *A model-vs-model arms race concedes the capability is the product, which it isn't.*
- ▫️ We'd ship faster and add more features — *Speed and feature count aren't a wedge; a better-funded competitor matches both.*
- ✅ The capability is commodity; the wedge is how we apply it — e.g. grounding every explanation in the student's own curriculum with citations, so trust and fit are the moat — *The Tal Raviv/Aman Khan thesis; Perplexity's citation wedge is the template.*

**Q3 🟡 — Segmenting users for a v1 AI contract-review feature. Which choice best reflects AI product sense?**

- ▫️ The largest segment by reach, to maximize impact — *Reach-maximizing ignores the AI-specific constraint: you need a segment whose tolerance for imperfection you can verify.*
- ✅ A segment whose tolerance for probabilistic quality you can verify — e.g. an in-house legal team using it as first-pass triage with a human reviewer, not solo external advice — *In AI, "underserved" means "tolerates probabilistic quality"; a verifiable tolerance + HITL is the senior call.*
- ▫️ Whichever segment the sales team is closest to closing — *Sales proximity is a GTM convenience, not a product-sense rationale.*

**Q4 🔴 — Klarna's AI-only support push (claimed the work of 700 agents) reversed in 2025. Which lens failure best explains it?**

- ▫️ Economics — the model was too expensive per call — *Cost wasn't the stated problem; quality was.*
- ▫️ Go-to-Market — they targeted the wrong segment — *The segment was right; the failure was treating a quality tail as acceptable.*
- ✅ Trust & Liability — a catastrophic quality tail was mis-classified as a routine, retrievable failure, with no designed recovery — *AI can fail catastrophically; the Trust & Liability lens exists to force a designed recovery for the tail Klarna ignored.*

**Q5 🔴 — A candidate runs CIRCLES flawlessly but never mentions hallucination, eval, or cost-of-error. Per Exponent's 2026 guidance, how does this read?**

- ▫️ As a top answer — a clean framework is what interviewers want — *Pre-2024, maybe; in 2026 AI tools generate structured answers trivially.*
- ✅ As a 2024 PM — structured is now the floor; the missing AI-specific layer (failure mode, trust pass, cost-of-error) is where the senior signal lives — *Exactly Exponent's "structured is the floor, not the ceiling."*
- ▫️ As a fail — using CIRCLES at all is a red flag — *CIRCLES is fine as a starting structure; the issue is stopping there.*

---

## Trust & failure design

**Q6 🟡 — Your assistant answers from a knowledge base but gives confidently wrong answers when the source doc was updated last week and the index is stale. PAIR bucket + fix?**

- ▫️ User error — add input guidance — *The input is reasonable; the user isn't at fault.*
- ▫️ System error — route to a stronger model — *The model faithfully used stale context; a stronger model on stale data still gives the wrong answer.*
- ✅ Context error — the world changed; fix with retrieval grounding, a freshness/"last verified" signal, and a stale-data warning — *Drift/stale docs are the textbook context-error case.*

**Q7 🟡 — A PM says "let's remove the 'I don't know' response — it makes the product look weak, and coverage looks better without it." Best pushback?**

- ✅ Refusal is the cleanest graceful failure (Anthropic's "Unknown"); it's a deliberate coverage-for-trust trade — removing it means confident hallucination on every out-of-corpus question — *Abstention bounds the cost of error.*
- ▫️ Agree — always answering improves the demo and the coverage metric — *This optimizes a vanity metric while raising the cost of error on every out-of-corpus question.*
- ▫️ Keep it but hide it behind a setting most users won't find — *Burying abstention defeats its purpose.*

**Q8 🟡 — Microsoft Recall shipped continuous screen capture default-on and was pulled to opt-in. Through the trust lens, the core mistake?**

- ▫️ The feature was too slow — *The backlash was about privacy and consent, not latency.*
- ✅ It mis-classified capability acceptance as user consent — a trust/context miscalibration with no graduated, opt-in switch — *A system-level capability needs a graduated trust surface (opt-in), not a default-on rollout.*
- ▫️ The model hallucinated screen contents — *The problem was the capture surface and consent model, not output quality.*

**Q9 🔴 — Your team adds thumbs up/down to an AI feature. Beyond UX politeness, the highest-leverage use of that signal?**

- ▫️ Show an aggregate satisfaction score on a dashboard — *Read-only reporting; it doesn't improve the model.*
- ▫️ Email the user to thank them — *Good UX, but it doesn't capture the engineering value of the labeled signal.*
- ✅ Treat it as RLHF-grade labeled feedback — route corrections into the eval set and gate the next model change on whether it improves them — *Redpoint's "customers are your RLHF annotators."*

**Q10 🔴 — Adobe launched Firefly to enterprises foregrounding "commercially safe" (training-data discipline + IP indemnification). What trust problem does that solve that a UI change cannot?**

- ▫️ It makes images generate faster — *Indemnification is a legal/commercial posture, not a latency lever.*
- ✅ It reframes the ship threshold to "is the customer indemnified if it isn't perfect?" — moving residual risk off the customer onto a contract — *For enterprise buyers, calibrated trust is partly contractual.*
- ▫️ It guarantees the model never produces an infringing image — *No model guarantees zero infringement; indemnification is the mechanism for the residual case.*

---

## Prioritization under uncertainty

**Q11 🔴 — Ranking three AI bets with RICE. A: huge reach, but a wrong answer could expose customer financial data. B: modest reach, bounded cost-of-error, eval pass-rate 92%. How should AI-adjusted RICE treat A vs B?**

- ▫️ A wins — reach dominates — *The reach-first trap AI-adjusted RICE overrides; reach multiplies a catastrophic tail.*
- ▫️ They tie — cost-of-error isn't part of RICE — *In AI-adjusted RICE it's an explicit new row precisely so it can change the ranking.*
- ✅ B can rank higher — the cost-of-error row down-weights A's reach-multiplied catastrophic tail, while B has bounded harm and eval-backed confidence — *The new row stops a high-reach, catastrophic-tail feature from out-scoring a bounded, eval-confident one.*

**Q12 🟡 — An exec asks "what accuracy do we need before we launch the assistant?" Best reframing?**

- ▫️ "We'll launch at 99% accuracy to be safe." — *A single accuracy number is a non-answer for a probabilistic product.*
- ✅ "We launch at eval saturation — when it passes every solvable task on the slice we care about — with a guardrail kill-switch and a forgiving segment first." — *Eval saturation (Anthropic) is the published, defensible threshold.*
- ▫️ "We'll know when the demo feels good." — *Demo-feel is the magic-in-demo/languishes-in-production failure mode.*

**Q13 🟡 — A support-bot PRD lists the success metric as "90% model accuracy on our test set." Biggest gap?**

- ▫️ The target is too low; it should be 95% — *Raising the number doesn't fix the structural gap.*
- ✅ It conflates model accuracy with user value and names no business outcome — it needs an input metric, an output metric (e.g. deflection), and a guardrail — *One input, one output, one guardrail is the senior shape.*
- ▫️ It should specify which model and provider are used — *Model choice is an implementation detail.*

**Q14 🟡 — Intercom routes between GPT-4 and GPT-3.5 per scenario. What prioritization principle does this encode?**

- ▫️ Always use the most capable model for consistency — *Ignores the cost dimension.*
- ▫️ Pick one model globally and optimize prompts around it — *Forfeits the per-request cost/quality optimization.*
- ✅ Apply ICE/RICE to model selection per request — cheap model where cost-of-error is low, expensive where it isn't — *Dynamic routing is the textbook AI cost/quality move.*

**Q15 🟡 — Your team wants an analytics dashboard for the new AI feature before the core flow is solid. Per Redpoint/Tome, the senior call?**

- ▫️ Build the dashboard first — you can't manage what you can't measure — *Inverts the priority; Redpoint/Tome flag dashboards as the secondary surface to defer.*
- ▫️ Build both in parallel to avoid rework — *Parallel work dilutes focus, the scarce resource.*
- ✅ Prune it — ship the critical core first; "do not build dashboards" is the canonical example of resisting the feature flood — *Pruning is the prioritization act.*

---

## AI PRDs & acceptance criteria

**Q16 🟡 — A reviewer says your AI PRD's success-metrics section is fine because it lists "≥92% model accuracy." Structural problem?**

- ✅ Quality bar (model behavior) and success metrics (business outcome) are being conflated — they belong in separate sections; success metrics need a business outcome like adoption or deflection — *One of the two load-bearing design choices in the synthesized structure.*
- ▫️ Nothing — a clear accuracy bar is what an AI PRD needs — *A model-behavior bar alone isn't the success-metrics section.*
- ▫️ The accuracy should be a letter grade for executives — *Letter grades are less precise; the issue is the missing business layer.*

**Q17 🔴 — Write one acceptance criterion for a feature that summarizes support tickets. Which is a proper probabilistic acceptance criterion?**

- ▫️ "The summary should accurately capture the ticket." — *Unmeasurable and un-lintable — the canonical red-flag criterion.*
- ✅ "On a 2,000-ticket gold set, ROUGE-L ≥0.45 AND an LLM-as-judge rates faithfulness ≥4/5 on ≥90% of samples, decomposed by ticket category, refreshed monthly." — *Metric + threshold + slice + cadence, and executable in CI.*
- ▫️ "The summary should be fast and high-quality." — *"Fast isn't a target" and "high-quality" is an adjective.*

**Q18 🟡 — Your PRD has numeric model bars but no guardrail table. What's missing for "deployment-ready"?**

- ✅ A two-direction guardrail table — input (PII, prompt-injection, topic filters) and output (toxicity, hallucination, format/brand-voice) — plus gateway vs evaluation-level layering — *A missing guardrail table is a missing definition of deployment-ready.*
- ▫️ Nothing material — guardrails are an appendix concern — *Treating guardrails as an appendix is exactly the failure.*
- ▫️ A longer problem statement — *More framing doesn't make a probabilistic system safe to deploy.*

**Q19 🟡 — Two months post-launch your AI PRD no longer matches the product (model upgraded twice, requirements shifted). What does the 12-section structure prescribe?**

- ▫️ Freeze the PRD at launch for a stable reference — *A frozen PRD is a wrong PRD by month two.*
- ✅ A Living Epilogue — a running decision log (Date / Decision / Reason / Reversible?) updated weekly, plus a fixed eval-refresh cadence — *Requirements evolve as fast as the model.*
- ▫️ Rewrite the entire PRD each quarter — *Wasteful and loses decision history.*

**Q20 🟡 — Shopify Auto Write's PRD set the launch metric as "15% merchant adoption within 180 days." Why stronger than "increase merchant engagement"?**

- ▫️ It's a bigger, more ambitious number — *Ambition isn't the point.*
- ▫️ It avoids needing an eval set — *A business target complements, not replaces, model-behavior bars.*
- ✅ A single dated, named-persona target prevents the "magical in demo, languishes in production" failure by making one team accountable to one measurable outcome by a date — *Vague engagement metrics can't be missed or owned.*

---

## Exec comms & narrative

**Q21 🟡 — 10 minutes with the exec team to pitch an AI support co-pilot. Best opening?**

- ▫️ Start with the architecture: the RAG pipeline, the model, the eval harness — *Leading with architecture is the canonical way to lose an exec room.*
- ✅ Open with a named customer and their workflow, quantify the problem in hours/dollars, then present the bet as a hypothesis with thresholds and ask for fund/kill/study — *The briefing arc that survives a skeptical CFO.*
- ▫️ Lead with how this keeps the company competitive against AI-native rivals — *Competitive fear is vague and unfalsifiable.*

**Q22 🔴 — "Why does storytelling matter MORE in the AI era, when AI can write the deck for you?" Strongest answer?**

- ▫️ It doesn't — AI writing the deck means comms matters less — *Inverts Gothelf's thesis.*
- ▫️ Polished slides always win deals — *Polish without substance is "storytelling as theatre."*
- ✅ AI commoditizes the surface (the deck), so the differentiator becomes the evidence the room can grip — the eval set, labeled examples, and named user that prove real expertise — *Gothelf's exact argument.*

**Q23 🟡 — Your AI PRD is headed to the C-suite. Per Korn Ferry, which three objections should it pre-empt?**

- ▫️ "Is it on brand / on time / on budget?" — *Delivery-management concerns, not the AI-specific risk/value questions.*
- ✅ "Is it safe?" (guardrail table), "Does it work?" (numeric bars on a gold set), "Is it worth it?" (business metric + horizon) — *Each answered by a section already in the synthesized AI PRD.*
- ▫️ "Which vendor / cloud / framework?" — *Enabler-level details.*

**Q24 🟡 — A skeptical exec asks "why did you pick this model over the cheaper one?" What artifact answers in 15 seconds?**

- ✅ A living decision log (Date / Decision / Reason / Reversible?) that records the trade-off and whether it's cheap to unwind — *Built precisely so the "why behind each what" is scannable.*
- ▫️ The full eval report appended at the back — *A raw dump isn't scannable in 15 seconds.*
- ▫️ A verbal explanation from memory — *Slower, less consistent, not auditable.*

**Q25 🔴 — Behavioral: "Tell me about an AI product decision you got wrong." Highest-scoring answer per Aakash's three laws?**

- ✅ "We shipped a summarizer with no hallucination guardrail; faithfulness dropped to ~80% on long docs, tickets spiked 12%, so I added a grounding check + abstention and a faithfulness eval gate before the next release." — *Specific metric + missing guardrail + structured fix, with ownership.*
- ▫️ "We had to roll back a feature once because it wasn't working well." — *Vague rollback stories lose the largest-weight round.*
- ▫️ "The model just wasn't good enough yet, so we waited." — *Deflects ownership.*

---

## The capstone case (agentic PRD)

**Q26 🟡 — Outlining the agentic personal-shopper PRD with 30 minutes. Where do you spend the most tokens?**

- ▫️ A thorough problem statement and market sizing — *The strategic-foundation floor; over-investing there leaves the AI-specific half thin.*
- ✅ The AI-specific cluster — eval framework, numeric quality bar, guardrails, and the human-confirmed action gate — *A PRD round is graded on evaluation rigor and risk/trust.*
- ▫️ A polished GTM and pricing plan — *One section of the operational plan.*

**Q27 🟡 — Choosing the architecture for the shopping agent. Best-reasoned answer?**

- ▫️ A multi-agent system (planner, searcher, negotiator, buyer) for maximum capability — *Inverts Anthropic's rubric; unnecessary orchestration adds failure surface.*
- ✅ Start with a single-purpose recommend-and-compare tool grounded in catalog data; introduce multi-step agency only when the eval shows it fails on genuinely multi-step tasks — *Anthropic: start simple, add agency only when simpler solutions fail.*
- ▫️ Whatever framework is most popular this quarter — *Not an architecture rationale.*

**Q28 🟡 — For v1 of the agent, where do you place the human-in-the-loop, and why?**

- ✅ At the irreversible step — the agent drafts the cart, but a human confirms before any purchase is charged — *The action gate bounds the catastrophic tail; everything up to the cart is recoverable.*
- ▫️ Nowhere — full autonomy is the point — *Autonomous checkout in v1 mis-prices the cost-of-error tail.*
- ▫️ At every step, requiring approval for each recommendation — *Destroys the product value; the gate belongs at the irreversible action.*

**Q29 🔴 — "Three weeks after launch, recommendation quality dropped 15% and you shipped nothing." Most likely cause and safeguard?**

- ▫️ Random variation — LLMs are probabilistic — *A sustained drop with no deploy isn't noise.*
- ▫️ The user base changed; wait for it to normalize — *Doesn't explain a broad sustained drop, and "wait" isn't a safeguard.*
- ✅ The provider silently updated the model — pin model + prompt versions and gate every change on an eval — *The silent-regression pattern; pinned versions + a gold-set gate catch provider-side changes.*

**Q30 🔴 — Across the whole capstone round, what single rubric signal most reliably separates strong from weak?**

- ▫️ The most complete, perfectly-structured framework applied end-to-end — *Structure is the floor in 2026.*
- ✅ Naming a specific metric, a specific failure mode, and a specific user — *The research's one-line heuristic across every round.*
- ▫️ Citing the newest models and highest benchmark scores — *Benchmarks aren't product sense.*

---

## Real-loop product-sense questions

> **✅ Reported** unless labeled 🔮. Run each through the [six lenses](../frameworks/README.md#2-the-six-lenses--ai-product-sense); lead with the wedge, the segment, and the cost-of-error.

**Q31 ✅ Reported 🟡 — "What goal would you set for an AI-only social network that OpenAI is building?"** — [IGotAnOffer](https://igotanoffer.com/en/advice/ai-product-manager-interview) · mock walkthrough by [IGotAnOffer (ex-Google PM)](https://www.youtube.com/watch?v=SWlcKSuoFJA).

**Q32 ✅ Reported 🔴 — "Company goal +30% engagement — where would you deploy AI, and how would you prove it beats a non-AI approach?"** — [IGotAnOffer](https://igotanoffer.com/en/advice/ai-product-manager-interview). The tell: a head-to-head A/B vs the rule-based baseline with a falsifiable hypothesis.

**Q33 ✅ Reported 🟡 — "Evaluate whether AI is the right solution vs a traditional approach."** — [Crosschq](https://www.crosschq.com/blog/ai-product-manager-interview-questions). Apply PAIR's unique-value test: if a heuristic clears the bar, ship the rule.

**Q34 ✅ Reported 🔴 — "OpenClaw: 2M weekly users, $15K/mo API bleed, no team — make four decisions in 48 hours."** — [The AI-Native PM](https://theainativepm.com/cases). Lead with **routing/cost** (the bleed) and a **cost-per-task** North Star.

**Q35 ✅ Reported 🔴 — "Should an AI PM at Netflix ship AI-generated movies to 325M subscribers using Seedance 2.0?"** — [The AI-Native PM](https://theainativepm.com/cases). A Trust & Liability + brand-risk case: price the tail (quality, IP, subscriber trust) before the capability.

Also ✅ Reported ([IGotAnOffer](https://igotanoffer.com/en/advice/ai-product-manager-interview)): *"Design a smart fridge for Google."* · *"What industry could benefit most from enterprise ChatGPT?"* · *"What is your favorite AI product, and why?"* · *"Given an AI-powered product facing usability challenges, how would you improve it?"*

---

*More: [technical-fluency](technical-fluency.md) · [metrics](metrics.md) · [ai-specific-cases](ai-specific-cases.md) · [behavioral](behavioral.md) · [index](README.md). See [worked answers](../answers/) for full case walkthroughs.*
