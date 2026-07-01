# Worked Answer 02 — AI resume-screener (and keeping it fair)

**Prompt (Product Sense with AI + responsible-AI round):** *"Design an AI feature that screens resumes for a high-volume role — and make sure it's fair."*

> [!NOTE]
> This case is a trap for two reasons: recruiting is a **regulated, bias-sensitive** domain (a wrong screen is an EEOC problem, not an annoyance), and it *looks* like a clean classification task. The senior move is to refuse full automation and design the fairness instrumentation *into* the feature.

---

## 0. Scope first

- **User & job?** Recruiters processing thousands of applications; the job is to *surface* strong candidates and *reduce time-to-first-review*, not to reject anyone automatically.
- **Autonomy?** **Rank + explain, never auto-reject.** A human makes every advance/reject decision.
- **Stakes?** Irreversible + regulated (a wrongly-filtered candidate is invisible harm + legal exposure).

## 1. Cost-of-error tail

The tail is **systematic bias against a protected group at scale** — reach *multiplies* it. So (a) autonomy stops at ranking, and (b) a per-group fairness gate is a launch-blocking criterion, not a monitoring nice-to-have.

## 2. The six lenses (abbreviated)

- **Model Behavior** — reliable at *extracting and matching stated qualifications*; **not** reliable at judging "culture fit" or inferring anything not on the page. Scope it to structured, defensible criteria only.
- **System Design** — the LLM is one step: deterministic parsing pulls structured fields; the model scores *against an explicit, job-related rubric*; a validator strips any proxy signal (name, school, zip) from what the model sees.
- **Trust & Liability** — the wedge is **explainability**: every rank comes with the *job-related reasons*, so a recruiter (and an auditor) can check it. An unexplained score is a liability.

## 3. The wedge

Not "an AI that picks the best candidates." The wedge: **a transparent, rubric-grounded ranking that surfaces the top slice with a checkable rationale per candidate, with a human deciding every outcome** — and a fairness audit baked into the eval.

## 4. Fairness design (the part the round is really testing)

- **Blind the proxies.** Strip name, gender-coded terms, school prestige, and location from the model's input; score only on job-related qualifications.
- **Stratify the eval by protected group.** The golden set is decomposed by demographic slice; a regression in one slice can't hide behind a healthy average.
- **Set a disparate-impact floor.** Ship-gate: the selection rate for any group stays within the four-fifths rule of the highest-selected group on the eval set. Fail it → don't launch that slice; audit and fix.
- **Human-in-the-loop by construction.** No auto-reject; the model *ranks and explains*, the recruiter decides.
- **Close the loop.** Recruiter overrides feed the eval set; the audit re-runs on a fixed cadence with a named owner (fairness is *ongoing*, not a launch checklist).

## 5. Success metrics

```
Business:   30% reduction in time-to-first-review; no drop in downstream hire quality (90-day).
Model bar:  >=90% agreement with recruiter rankings on the golden set, decomposed by slice.
Fairness:   selection-rate ratio >= 0.8 across protected groups (four-fifths rule) on the eval — HARD GATE.
Guardrails: input strips protected proxies; output = a job-related rationale per candidate.
Divergence alarm: weekly per-group fairness gate, independent of the time-saved metric.
```

## 6. Launch gate

Shadow-rank against past cycles with known outcomes → audit the fairness slice → canary with recruiters → gate expansion on the fairness floor *and* the ranking-agreement lower CI bound. Rehearsed rollback. A **Risk Report** (categories × levels × mitigations × residual × rollback) attached to the release, mapping the NIST GenAI-profile risks (bias, human-AI configuration) to the eval cases above.

---

## Follow-up pushes

- *"The model is 92% accurate — ship it fully automated?"* → No. Accuracy is not the constraint; the irreversible, regulated fairness tail is. Ranking + human decision, always.
- *"Fairness looks fine on average."* → Averages hide slice regressions; show the per-group gate, not the aggregate.
- *"What if a group has too few examples to measure?"* → That's a coverage gap — don't ship to that slice until the golden set covers it; shadow-serve and collect.

**Framework applied:** [Six Lenses](../frameworks/README.md#2-the-six-lenses--ai-product-sense) · subgroup/fairness audit ([content/05 §responsible-AI](../content/05-evaluation-and-responsible-ai-launch.md#4-responsible-ai-gates--build-it-in-dont-audit-for-it)) · [trust design](../content/07-designing-human-ai-interaction.md).

*[← Previous](01-ai-feature-clinic-messaging.md) · [Answers index](README.md) · [Next: eval harness →](03-eval-harness-support-assistant.md)*
