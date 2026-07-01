# Contributing to ai-pm-interview-prep

Thanks for helping build the best AI PM interview-prep resource on GitHub. This is a **quality-first** repo — a PR that adds one well-sourced, well-annotated item beats one that dumps ten bare links. Here's the bar.

---

## What we accept

- **Real interview questions** — with a source and a provenance label.
- **Resources** — typed, annotated, and license-noted.
- **Worked answers** — original, framework-backed, senior-grade.
- **Corrections** — outdated prices, dead links, a framework that's moved on, a company loop that changed.

## What we don't accept

- Bare link dumps (every link is typed and annotated, capped ~8 per topic).
- Verbatim copies of CC-BY-SA or non-permissive content (link + commentary only).
- "Representative" questions passed off as reported. Label honestly.
- LLM-generated filler with no source, no metric, no failure mode.

---

## The quality spec

### Every real question needs a provenance label
- ✅ **Reported** — asked in a real loop. Attach the source URL.
- 🔮 **Representative** — synthesized from patterns. Say so.

Never label a synthesized question as Reported. Calibration is the whole point of the bank.

### Every multiple-choice checkpoint needs a per-option "why"
The value is in the reasoning, including the *wrong* options — each distractor should encode a real interview trap (the bigger-model reflex, "it depends," reach-first RICE, thumbs-up-as-truth). Mark the correct option with ✅.

### Every link is typed, annotated, dated, and (for repos) license-noted
```
- 📰 [Title](url) — Author/Org, Date. One sentence on what it's for and why it earns the slot.
- 💻 [Repo](url) — what it's for. License: MIT · ~1.9k★.
```
Types: `📄 paper · 📘 docs · 🎬 video · 📰 article · 💻 repo · 📕 book`. Cap ~8 per topic.

### Every worked answer applies a named framework
Lead with the cost-of-error tail, name a specific metric + failure mode + user, and end on an eval + a gate. See [/answers](answers/) for the house shape.

### Voice
Sharp, senior, concrete, answer-first. Interview-angle everywhere. No fluff, no hype. Dated 2026.

### License discipline
This repo is **MIT**. Only adapt MIT / Apache-2.0 / CC0 sources (with attribution — License · Source · Author). CC-BY-SA and non-permissive sources are **link + commentary only, never copied verbatim.**

---

## How to contribute

1. Open an issue using a template ([add a question](.github/ISSUE_TEMPLATE/add-a-question.md) · [add a resource](.github/ISSUE_TEMPLATE/add-a-resource.md)) or a PR.
2. For a PR, follow the [PR template](.github/PULL_REQUEST_TEMPLATE.md) and check the boxes.
3. The [link-check workflow](.github/workflows/links.yml) runs on every PR — fix any dead links it flags.
4. Keep the diff focused: one question set, one resource cluster, or one answer per PR.

---

## Structure map

| Path | What lives here |
|---|---|
| `README.md` | the thin index — the loop map, TOC, FAQ. Real content lives below. |
| `content/` | one concept per file (technical fluency, product sense, PRDs, metrics, eval/launch, model selection, human-AI interaction) |
| `frameworks/` | the reusable rubrics with worked examples |
| `questions/` | the tiered bank, grouped by category, with per-option whys + provenance |
| `answers/` | original worked answers to AI-specific case prompts |
| `resources.md` | the typed, license-noted resource hub |

Thanks for keeping the bar high. — Maintained by [Landed](https://landed.jobs).
