# 01 · AI Technical Fluency for PMs

*How LLMs, RAG, agents, and evals actually work — enough to reason like a senior AI PM, with zero coding. Updated 2026-07.*

> [!NOTE]
> **The bar this clears.** Foundational AI-PM screens test whether you treat the model as a *system with real mechanisms and limits* — or as a magic box you sprinkle onto a feature. Every answer below leads with the **mechanism**, then the **product implication**. That order is the senior tell.

---

## The one-paragraph mental model

An LLM is a **probabilistic next-token predictor with finite working memory you rent by the token, and latency that scales with how much it writes.** It reads your text as **tokens** (sub-word pieces), scores every token in its ~100k–200k vocabulary, *samples* one, appends it, and repeats until a stop token. Everything we call "reasoning," "instruction-following," or "knowing things" is emergent behaviour on that loop. Almost every scoping, pricing, and reliability decision — and almost every technical-round question — falls out of those three facts.

The first product consequence: **the output is a sample from a distribution, not a return value.** The same prompt can and will produce different text.

---

## 1. LLMs: tokens, context, and why output varies

**Why it "knows" anything.** The weights are a **lossy compression of training data at a fixed point in time.** Three product implications ride on that one sentence:

- **Knowledge cutoff** — it has no row for your Q3 board deck; yesterday is genuinely unknown to it.
- **Cannot cite by default** — it reconstructs plausible text, it does not look anything up.
- **Confidently invents** — when the right answer was never in the weights, it fills the gap with fluent plausibility. That is a **hallucination**, and it is a *property you design around*, not a bug you patch.

> [!TIP]
> **Interview angle — "Why does the model hallucinate on our internal data?"** The strong opener: *the data was never in the weights, so the model fills the gap with fluent plausibility — which is why the fix is retrieval, not a bigger model.*

**Tokens are the unit of cost, latency, and limits.** Rules of thumb worth memorising: **~4 characters ≈ 1 token**, **~0.75 words per token** (≈750 words ≈ 1,000 tokens). A 20-page PDF ≈ 12,000–15,000 input tokens. Code, JSON, tables, and non-English text tokenize *less* efficiently, quietly inflating cost. Two pricing facts to keep in your pocket:

1. **Output tokens cost ~3–8× input tokens** — decode is sequential and compute-heavy; reading the prompt (prefill) is parallel and cheap. *The model writing a lot is what gets expensive.*
2. **A stable prompt prefix can be cached for up to ~90% off its input cost.**

So the cheapest feature is a **big cached prompt with a short, structured output** — the opposite of the naive "shorter prompts are cheaper" intuition.

> [!WARNING]
> **Senior trap — the tokenizer, not the reasoning.** "How many r's in strawberry?", reversing strings, and long digit-by-digit arithmetic are shaky because they *fight the tokenizer*, not because the model can't reason. The fix is a **tool** (code execution, a calculator), never a bigger model.

**The context window is working memory, and more is not better.** The system prompt, history, retrieved docs, tool definitions, tool results, *and* the output all share one budget (say 200k tokens). Nothing persists between calls unless your product re-sends it — a chatbot doesn't "remember"; your code re-sends the history every turn and you pay for it every turn. Two empirical facts overturn "more context is better":

- **Lost in the Middle** (Liu et al., 2023) — recall is U-shaped; facts at the *start* or *end* are recalled reliably, facts in the *middle* are frequently missed.
- **Context Rot** (Chroma, 2025) — answer quality degrades as input grows, *even in frontier models*, and it's a reasoning degradation, not just a retrieval miss.

The upshot you repeat in scoping debates: **stuffing more into the prompt can make answers worse, not just slower and pricier.** The window is a scarce, *ordered* resource — curate what goes in, put what matters at the edges.

**Why the same input varies — temperature.** Low temperature → greedy, consistent, repetitive (extraction, classification, anything you parse). High temperature → varied, "creative," prone to wander (brainstorming, multiple drafts). But **temperature 0 is not a determinism guarantee** — batched inference, GPU floating-point quirks, mixture-of-experts routing, and *silent provider updates* all vary the output. Never `assert output == expected_string`; constrain to a schema and validate.

**Capability tiers — match the tier to the cost of error.**

| Tier | Reliability | Tasks | What it needs |
|------|-------------|-------|---------------|
| **1** | ~90%+ today | summarize, classify, extract, draft, Q&A over *provided* context | light scaffolding |
| **2** | ~60–85%, context-dependent | synthesize across docs, plan, multi-step reasoning | retrieval + evals + a human path |
| **3** | genuinely risky | reliable multi-step **agency**, causal forecasting, long-horizon execution | hard human-in-the-loop scaffolding |

Scope around the **cost of error** (reversible / expensive / irreversible / regulated), never around the cleverness of the demo. A tier-3 experience with tier-1 scaffolding *is* the product bug.

> [!TIP]
> **"How do you launch a probabilistic, sometimes-wrong feature?"** Name the failure modes (hallucination, calibration error, stale knowledge), then design the CX around them — confidence cues, citations, an easy correction path, escalation — and ship a quality metric. Don't apologise for the model; engineer the product around it.

**Production evidence.** Notion cut AI-chat latency ~4× (2s → ~350ms) for 100M+ users by serving a *smaller fine-tuned model on the hot path* — "latency is perceived as quality." GitHub Copilot caps prompts (~6k chars) to stay inside its latency envelope. Klarna claimed an agent did the work of 853 employees, then walked back the AI-only strategy when quality fell — the demo succeeded, the long tail (escalations, disputes) was underweighted.

---

## 2. Prompting is context engineering

A prompt is **the entire context you assemble for one call** — instructions, examples, retrieved data, tool definitions, and the output contract. It's the highest-leverage, lowest-cost lever you have *before* RAG or fine-tuning. In-context learning means the model adapts from what's in the prompt with **zero weight change** — you're steering which region of its learned distribution you sample from.

**Anatomy of a strong prompt** (ordered; not every call needs every part): **role** → **task** (one imperative) → **context/data** (delimited!) → **constraints** (do / never / length / audience) → **examples** (few-shot) → **output contract** (the structure to return).

Two mechanisms interviewers push on:

- **Delimit data from instructions** — everything in the window is just tokens; un-delimited user input can be read as a *command* (the seed of prompt injection). Wrapping data in clear sections says "content to act on, not instructions to follow."
- **Say what *to* do, not "don't"** — a negation still primes the forbidden concept. "Respond only in formal English" beats "don't be casual."

> [!TIP]
> **Few-shot vs fine-tune.** Few-shot **pins shape and tone** at zero training cost, and is instantly editable — but it does *not* teach new facts (that's retrieval and fine-tuning). Fine-tune only when the examples needed to hit quality no longer fit the context, or the per-call token cost stops penciling at your volume.

**Structured output is a contract, not a nicety.** A JSON shape turns a probabilistic text generator into something you can *render, validate, route on, and evaluate field-by-field* — and it narrows scope, making the feature predictable. *A free-text answer is a demo; a structured answer is a feature.*

> [!WARNING]
> **Prompts are product surface area.** A one-word change moves quality up or down across millions of calls — silently. Anthropic's April 2026 Claude Code postmortem traced a wave of "it got dumber" reports partly to a verbosity-reduction system-prompt tweak. **Version prompts like code and gate every change with an eval.** Eyeballing one output is how silent regressions ship.

**A PM's prompt-review checklist** — diagnose failures by named mode:

| Symptom | Likely cause | Fix |
|---|---|---|
| inconsistent across users | ambiguous task | state one imperative |
| ignores a key rule | buried constraint | move it to the top, make it explicit |
| wanders / too long | no length/format limit | add an output contract + word cap |
| parser breaks | free-text output | enforce a strict JSON schema |
| confident wrong facts | no grounding data | add retrieval; allow "I don't know" |
| good→bad after edits | no eval / regression | pin the prompt version, gate with evals |

---

## 3. RAG vs fine-tuning vs API — the decision you cannot fumble

This is the **highest-frequency technical round** for an AI PM. Interviewers want a **decision tree with a committed default**, not "it depends."

**What each lever changes** — the distinction they test:

| Lever | Changes | Best for | Cost / effort |
|---|---|---|---|
| **Prompting** | the INPUT | open-ended gen, format, tone | lowest (instant) |
| **RAG** | the CONTEXT | fresh/proprietary facts, citations, access control | medium |
| **Fine-tuning** | the WEIGHTS | consistent tone/format, a narrow skill, lower per-call cost at scale | high |
| **Custom train** | the MODEL | a domain moat (huge proprietary corpus, specialized vocab) | highest (rarely justified) |

**Default order: `prompt → RAG → fine-tune → custom`, each rung gated by an eval.** It's a *ratchet you climb only when forced*, not a menu. The burden of proof is on *moving up*, and the proof is an eval showing the cheaper rung plateaued.

- **RAG** = the default for knowledge that moves. Three structural wins: **freshness** (re-index, no retrain), **attribution** (cite the chunk), **access control** (filter by who can see what). IBM's decisive test: *if the answer changes because the world changes, RAG is right and fine-tuning is wrong.* Bonus: you can **delete a user's data from the index on request** — impossible once facts are smeared across frozen weights.
- **Fine-tuning** = buys **behaviour** (tone/format at scale, a narrow repeated skill, distilling to a cheaper/faster small model), *not facts*. `RAG adds knowledge; fine-tuning changes behaviour.` They **compose** — most production systems stack them.
- **Custom train** = the historical exception (BloombergGPT: 50B params, 700B-token finance corpus — a real vocabulary moat, March 2023, before cheap retrieval). Or the existential-trust exception (Harvey custom-trained on US case law: **+83% factual vs GPT-4, preferred 97%** — because RAG's retrieve-and-cite failure mode was itself the bottleneck).

**Build vs buy** underneath it all: default **buy** (commoditizing, non-core) behind a **clean abstraction layer** so a future build costs ~zero. Build only on a trigger (requirements change weekly, vendors immature/sunsetting, the real problem is workflow, integrations are the hidden complexity) — and treat **proprietary data as the structural build signal.** Notion *bought the model, built the data substrate* — the corpus and the retrieval system were the edge, not model weights.

> [!WARNING]
> **The classic miss.** Proposing fine-tuning to *inject knowledge* bakes in facts that go stale on every edit, gives no citations, and can't enforce per-user permissions. Reach for RAG. Equally damning: "it depends" with **no default and no named evidence** — interviewers read both as "hasn't actually made this call."

---

## 4. Agents & MCP — the word everyone misuses

**Workflows** orchestrate models and tools through *predefined code paths*. **Agents** are systems where the model *dynamically directs its own process and tool use*. Agents buy **dynamism** and you pay with **cost, latency, and unpredictability** — so *find the simplest thing that works, and add agentic complexity only when it demonstrably improves outcomes* (Anthropic).

Most "AI features" live in the named **workflow patterns** between one prompt and a full agent: **prompt chaining** (draft→critique→revise), **routing** (classify→handler), **parallelization** (run pieces, aggregate/vote), **evaluator-optimizer** (one produces, another grades and iterates). Reach for an **orchestrator-workers agent** only when you genuinely can't enumerate the sub-tasks up front.

**The decision test — three filters:**
- **Agency** — does the task need multi-step tool use the user wouldn't do themselves?
- **Reversibility** — if step 3 is wrong, can a human catch it cheaply, or is it irreversible (a refund, a deployment, medical triage)?
- **Value density** — is each task expensive enough to justify the orchestrator's error budget? Low density → a prompt is cheaper.

The senior tell is naming *where you put the human* on the irreversible steps.

**Compound failure — why multi-step agents are distributed systems.** If each step is 95% reliable, end-to-end is roughly `0.95^steps`:

| per-step reliability | steps | end-to-end |
|---|---|---|
| 0.95 | 1 | 95% |
| 0.95 | 5 | ~77% |
| 0.95 | 10 | ~60% |
| **0.99** | 10 | **~90%** |

A demo walks one happy path and looks flawless while every alternative path quietly degrades. The moves: **shorten the chain**, **raise per-step reliability**, **bound the blast radius** (drafts/recommendations until evals earn autonomy — *treat agents like junior interns*), and **instrument the trajectory** (step-level logging), because "the answer was wrong" is useless without "step 4 called the wrong tool."

**MCP (Model Context Protocol)** is the **integration standard, not a feature** — an open standard for secure two-way connections between AI tools and data sources (pre-built servers for Drive, Slack, GitHub, Postgres…). It replaces bespoke per-vendor integrations with **one reusable, portable protocol** — the USB-C of tool/data integration. The buy signal: *"does this vendor speak MCP?"* before adding another point integration.

> [!WARNING]
> **MCP widens the attack surface.** An agent that can call real tools can be steered by **prompt injection** — including *indirect* injection hidden in a document or webpage it reads. Contain with four controls: **least-privilege tools**, **input/output validation**, **human approval gates** on irreversible actions, and an **audit log**. Capability without containment is the liability.

---

## 5. Cost, latency & routing economics — a product problem you own

Routing *every* request to a frontier model can cost **$0.50–$2.00 per active user per day** — at scale, enough to consume a product's whole budget. Reason in four numbers per model: `$/1M input`, `$/1M output`, latency-to-first-token, context window — and make **cost-per-completed-task** (not cost-per-token) the North Star.

**Representative 2026 per-million-token prices** *(verify before quoting)*:

| Model | $/1M in | $/1M out | out/in |
|---|---|---|---|
| Grok 4.1 | 0.20 | 0.50 | 2.5× |
| Gemini 3 Flash | 0.50 | 3.00 | 6.0× |
| Claude Haiku 4.5 | 1.00 | 5.00 | 5.0× |
| OpenAI GPT-5.2 | 1.75 | 14.00 | 8.0× |
| Gemini 3.1 Pro | 2.00 | 12.00 | 6.0× |
| Claude Sonnet 4.6 | 3.00 | 15.00 | 5.0× |
| Claude Opus 4.8 | 5.00 | 25.00 | 5.0× |

Two facts to carry: **output costs ~5–8× input** (prune what it *writes*), and **the cheap-to-frontier spread is ~50× on input** (the right model per task beats any prompt tweak).

**The cascade / router — the highest-leverage move.** A published 2026 framework reaches **~97% of GPT-4 quality at ~24% of cost** under time constraints. A cheap model handles the easy 60–80% of traffic; a frontier model is called only on low confidence (cascade) or a hard-intent classification (route-by-intent). Back-of-envelope: 70% simple traffic routed to a model ~10× cheaper → blended cost ≈ `0.7×0.1 + 0.3×1.0 ≈ 0.37` of baseline. The play is not "buy a gateway" — it's **design the fallback**, and measure quality on a fixed eval set *per tier*.

**Hidden costs the naive estimate misses:** the **embedding / re-index bill** (re-embedding a corpus on every change is the classic RAG spike — *freshness has a recompute bill you schedule*), and **retry amplification** (timeouts, rate-limits, validation failures each trigger another full call).

**Latency — two numbers, different levers:** **TTFT** (time-to-first-token) tracks the *prompt* length → shrink/cache the prefix. **Total time** tracks the *output* length → shrink/stream the output, pick a faster model. **Streaming** masks latency — the cheapest perceived-speed win. Prompt caching cuts the stable prefix's input cost ~90% *and* reduces TTFT.

> [!WARNING]
> **"Engineering will figure out cost" ends the round.** At scale, cost and latency *are* the experience and the business model. Speak in `$/user/day` and `$/task`, design the routing fallback and the output cap, separate TTFT from total time, and put cost-per-task on a dashboard next to retention.

---

## 6. The capability-to-feature map (the capstone artifact)

Given a real product, map what models can *reliably* do onto a concrete feature set, and for each feature make five decisions: **architecture** (prompt/RAG/fine-tune/agent), **scope** (inputs/outputs/tasks/subjects), **cost & latency**, **failure design** (HITL?), and the **eval** (metric + golden set).

**The frame gate** — run *before* a feature earns a row:
1. Whose workflow changes, and what's the current workaround? → persona + baseline
2. Cost of error? reversible / expensive / irreversible / regulated
3. Capability tier needed? (tier 1 ship / tier 2 scaffold / tier 3 HITL)
4. Frequent + painful + AI-shaped enough? → ship / defer / kill
5. How will we know it worked *without trusting our demo*? → eval metric

Fail step 5 and the feature goes back for an eval plan, not forward.

**Scope on four levers** (NN/g): inputs, outputs, tasks, subjects. If any answer is "anything," it isn't scoped — a **bare chat box pasted onto your app** is broad on every lever and *success is indistinguishable from hallucination*. The MVP of an AI feature is **the smallest workload where you can measure model quality**, not the smallest feature set.

**Validate before you build** — the three-prototype rule: **(1)** Wizard-of-Oz (validate the problem + UX with a human faking the model), **(2)** golden eval set (50–500 hard/adversarial examples + LLM-as-judge + red team), **(3)** shadow/canary on 1–5% of traffic with a kill switch. Each stage gets a written go/no-go.

---

## 📚 Resources

*Typed, annotated, capped. `📄 paper · 📘 docs · 🎬 video · 📰 article · 💻 repo`. Dated where it matters.*

- 🎬 [Intro to Large Language Models](https://www.youtube.com/watch?v=zjkBMFhNj_g) — Andrej Karpathy. The canonical no-math tour of the generation loop.
- 📄 [Lost in the Middle](https://arxiv.org/abs/2307.03172) — Liu et al., 2023. The U-shaped recall finding you cite in every "the doc is too long" debate.
- 📰 [Context Rot](https://www.trychroma.com/research/context-rot) — Chroma Research, 2025. Why quality degrades as input grows, even in frontier models.
- 📘 [Claude prompting best practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/overview) — Anthropic. Roles, delimiters, do-not-don't, think-first.
- 📄 [RAG vs fine-tuning vs prompt engineering](https://www.ibm.com/think/topics/rag-vs-fine-tuning-vs-prompt-engineering) — IBM. The clean side-by-side + the "answer changes when the world changes" test.
- 📰 [Customizing models for legal professionals (Harvey)](https://openai.com/index/harvey/) — OpenAI. The custom-train case: +83% factual, preferred 97%.
- 📘 [Building Effective AI Agents](https://www.anthropic.com/research/building-effective-agents) — Anthropic, Dec 2024. Workflow-vs-agent taxonomy; start simple, add agency only when needed. *License: Anthropic docs — link + commentary.*
- 📘 [Introducing the Model Context Protocol](https://www.anthropic.com/news/model-context-protocol) — Anthropic. What MCP is and why integration economics shift.
- 📰 [AI API Pricing Comparison 2026](https://intuitionlabs.ai/articles/ai-api-pricing-comparison-grok-gemini-openai-claude) — IntuitionLabs. Source for the per-token table above.
- 💻 [Generative AI for Beginners](https://github.com/microsoft/generative-ai-for-beginners) — Microsoft. 21-lesson PM-friendly primer. *License: MIT · popular repo.*

---

**Next:** [02 · AI Product Sense](02-ai-product-sense.md) · Back to the [interview-loop map](../README.md#the-ai-pm-interview-loop-map) · Drill the [technical-fluency question bank](../questions/technical-fluency.md)
