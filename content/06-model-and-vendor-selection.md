# 06 · Model & Vendor Selection

*"What is your criteria in selecting a model?" is a verbatim technical-round question. Here's the senior answer — build-vs-buy, the decision hierarchy, routing, and reasoning in $/task. Updated 2026-07.*

> [!NOTE]
> **Model selection is not "pick the highest benchmark."** It's a product decision with margin, latency, trust, and reversibility baked in. Two PMs with the same model menu produce wildly different products. The senior instinct: **the model is the interchangeable part — one API call from being swapped — while the data integration and the eval harness are the proprietary moat.**

---

## The decision hierarchy (commit to a default)

Interviewers penalise "it depends" with no default. Commit:

```
prompt → RAG → fine-tune → custom-train
```

Each rung up costs more money, time, and maintenance, and locks in more — so the **burden of proof is on moving up**, and the proof is an eval showing the cheaper rung plateaued. Name the specific evidence that would make you deviate: freshness, tone-at-scale, a cost cliff, a proprietary corpus.

See [01 · AI Technical Fluency §3](01-ai-technical-fluency.md#3-rag-vs-fine-tuning-vs-api--the-decision-you-cannot-fumble) for what each lever changes.

---

## Build vs buy — the moat test

Default to **buy** (wrap an API), build only on a trigger (Marty Cagan × Hatchworks 2026):

| BUY (default) when… | BUILD when… |
|---|---|
| capability is commoditizing fast (STT, translation, basic embeddings) | requirements change weekly (roadmap = moat) |
| it's outside your core competency | vendors immature / keep sunsetting features |
| a clean API exists at your scale | the real problem is workflow + incentives |
| | integrations are the hidden complexity |
| | **your proprietary DATA is a structural edge** |

**The senior tier-2 move is optionality:** buy **behind a clean abstraction layer** (a thin model wrapper + an eval harness + a prompt-config) so swapping in a custom model later costs ~zero. Three tiers: **(1)** wrap API → **(2)** fine-tune on top → **(3)** train from scratch. Default (1); jump to (2) only when prompts plateau on tone/format/cost; (3) only with a measurable trust or unit-economics bar nothing else meets.

**The named cases** anchor the answer:

| Company | Decision | Why |
|---|---|---|
| **Notion** | bought the model, **built the data substrate** | the corpus + retrieval pipeline was the edge, not weights |
| **Harvey** | custom-trained on US case law | RAG's retrieve-and-cite failed on deep synthesis; existential trust stakes (+83% factual, 97% preferred) |
| **Bloomberg** | trained BloombergGPT from scratch (2023) | a genuine vocabulary moat + proprietary corpus, before cheap retrieval |

> [!TIP]
> **Before adding another bespoke integration, ask "does this vendor speak MCP?"** (see [01 §4](01-ai-technical-fluency.md#4-agents--mcp--the-word-everyone-misuses)). MCP is the *buy signal* for tool/data access — a reusable, portable protocol instead of N brittle point integrations.

---

## Selection criteria — the four numbers + the tiebreakers

Reason in **four numbers per model in play**: `$/1M input`, `$/1M output`, latency-to-first-token, context window. Then the tiebreakers:

- **Quality on *your* eval set** — not a leaderboard. Product sense is earned by using models on tasks you care about, not reading benchmarks.
- **Cost-per-completed-task** — the North Star (not cost-per-token, which rewards a cheap-but-wrong answer).
- **Latency envelope** — TTFT vs total time; can you afford a reasoning model in the loop?
- **Trust posture** — does the vendor offer indemnification / a trust layer / data-residency guarantees the segment needs?
- **Reversibility** — how locked-in are you? Keep the abstraction layer.

**Route, don't pick one globally.** Intercom routes between GPT-4/3.5 per scenario; a published 2026 cascade hits **~97% of frontier quality at ~24% of cost**. Cheap model on the easy 60–80%, frontier only where the cost-of-error justifies it. The play is **design the fallback**, then measure quality on a fixed eval set *per tier* — a router that silently sends hard requests to the cheap model degrades quality invisibly.

---

## "Make the current model cheaper, or invest in the next, more capable one?"

The weak answer is "better models win." The strong answer reasons about the whole P&L:

- **Margin** — a cheaper model widens it now.
- **Competitive pressure** — rivals are pricing against you, so a capability lead may be short-lived.
- **Model timeline** — frontier prices fall fast; today's expensive model is next quarter's mid-tier.

Capability lift is not the same as user-outcome lift or business value. Tie the choice to **retention and unit economics**, not a benchmark.

> [!WARNING]
> **"Train our own model to differentiate" for a commoditizing capability** (e.g. meeting summarization well-served by frontier APIs) is the classic over-build. Custom training is months of effort justified only by a real data/vocabulary moat; the frontier API will outrun your team. Buy behind an abstraction; let evidence — not ambition — trigger the build.

---

## Anthropic vs OpenAI vs Google vs xAI — a PM's framing

Don't memorize benchmark tables that expire monthly. Frame the choice by *what the product needs*:

- **Trust / safety posture & long-context reasoning** → Anthropic (Claude) — RSP, constitutional approach, strong on agentic + tool-use.
- **Ecosystem breadth & fastest feature velocity** → OpenAI (GPT) — widest tooling, structured outputs, assistants.
- **Multimodal + price/perf at scale & GCP integration** → Google (Gemini) — Flash tier for cheap high-volume.
- **Raw price + real-time search** → xAI (Grok) — aggressive input pricing.

The senior point: **the right model is the one that passes your eval on your task at your cost-per-task and your trust bar** — and you keep the abstraction so the answer can change next quarter.

---

## 📚 Resources

*Typed, annotated, capped. `📄 paper · 📘 docs · 📰 article`.*

- 📰 [The Build vs Buy Framework in the Age of AI](https://hatchworks.com/blog/gen-ai/build-vs-buy-framework/) — Hatchworks, Jan 2026. The 2026 build-trigger list.
- 📰 [Build vs Buy in the Age of AI](https://www.svpg.com/article-build-vs-buy-in-the-age-of-ai/) — Marty Cagan (SVPG). Core-competency framing + MCP-addressable components.
- 📰 [Anthropic vs OpenAI (2026)](https://www.lilbigthings.com/post/anthropic-vs-openai) — lilbigthings, Jan 2026. A current side-by-side to sanity-check, not memorize.
- 📰 [AI API Pricing Comparison 2026](https://intuitionlabs.ai/articles/ai-api-pricing-comparison-grok-gemini-openai-claude) — IntuitionLabs. Grok vs Gemini vs OpenAI vs Claude per-token.
- 📰 [The AI PM's Menu: cost-quality tradeoffs](https://generativeai.pub/the-ai-pms-menu-a-field-guide-to-cost-quality-tradeoffs-d897c9da746b) — Scarlett Zhao. Routing as the menu, not the default.
- 📄 [Dynamic Model Routing and Cascading](https://arxiv.org/html/2603.04445v2) — arXiv. The ~97% quality / ~24% cost result.
- 📰 [Enterprise AI Services: Build vs Buy](https://www.hp.com/us-en/shop/tech-takes/enterprise-ai-services-build-vs-buy) — HP, Jul 2025. Enterprise-buyer angle.

---

**Next:** [07 · Designing Human-AI Interaction](07-designing-human-ai-interaction.md) · Back to the [interview-loop map](../README.md#the-ai-pm-interview-loop-map) · Drill the [technical-fluency bank](../questions/technical-fluency.md)
