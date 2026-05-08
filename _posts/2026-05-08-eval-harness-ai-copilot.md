---
layout: post
title: "Notes From Building an Eval Harness for a Production AI Co-Pilot"
subtitle: "Why shipping LLM apps by vibes is more dangerous than it looks"
date: 2026-05-08
tags:
  - ai
  - engineering
  - llm
  - evals
  - prompt-engineering
---

Most LLM apps in 2026 ship by vibes — write a prompt change, read a few outputs, decide if it feels better, ship. The "decide if it feels better" step is the problem.

## The Control Experiment

Evaluate a baseline prompt against an identical copy of itself. No change. Nothing should regress.

Result: **9 of 12 scenarios judged as regressions. 25 dimensional regressions across the suite.** Pure judge variance, zero actual change to the prompt.

Trust your gut on those outputs and you'd roll back a perfectly good prompt 3 out of 4 times. That's not iteration — that's noise dressed up as decision-making.

The fix is an eval harness. Not the thing anyone wants to build.

## What This Isn't

This is **application-quality evaluation** — does this prompt change make the conversational behavior better or worse than baseline?

Not capability evaluation (METR-style autonomous-task benchmarks). Not safety evaluation (red-team prompt injection). The methods rhyme; the questions are different. Building one doesn't give you the others.

## Architecture — Three Concerns

About 1,000 lines of Python in `eval/compare.py` + `eval/judge_prompt.py`. Separate folder, separate dependencies, separate from the application code.

### Concern 1: Goldens, Not Transcripts

Twelve scenarios cover the surface area: cold-start onboarding, decision capture, dependency linking, edit flows, vague vs expert engineers, cross-domain edge cases.

Each golden defines **what must happen** and **what must not happen** — never the exact wording. String-match fails for conversational AI: many valid paths exist for any input.

```json
{
  "first_message": "...",
  "expected_outcomes": ["Recognizes the domain", "Moves to specific decisions in 2 turns"],
  "anti_patterns":     ["Treats a known concept as ambiguous"],
  "key_dimensions":    ["pacing", "specificity", "domain-fit", "..."]
}
```

Outputs are LLM-judged against a 17-dimension rubric — pacing, specificity, domain-fit, conversational coherence, and similar axes — scored Pass / Partial / Fail per dimension.

Goldens grow from bugs. When a real conversation surfaces a regression the eval missed, the broken behavior gets added as an anti-pattern. The suite hardens over time, in proportion to product use.

### Concern 2: Noise-Aware Judging

The control experiment proved single-run judging is unusable. The fix:

**Multi-run majority vote.** Default: 2 passes per side; `--thorough` runs 3 passes. Verdicts aggregate by majority — a regression has to be confirmed across runs, not flagged once.

**Confidence threshold.** If agreement across runs drops below 60% on a dimension, the dimension is classified Noisy and excluded from the verdict. The eval refuses to take a position when the signal isn't there.

(Defensive note: a 3-stage JSON parser strips markdown fences, fixes smart quotes / unescaped newlines / trailing commas, retries up to three times. Parse failures used to silently masquerade as regressions. They don't anymore.)

### Concern 3: Regression Taxonomy

Not every regression is equal:

| Class | Rule | Action |
|---|---|---|
| Hard regression | Pass → Fail | Always BLOCK |
| Soft regression | Pass → Partial, or Partial → Fail | REVIEW at 1–2 across the suite; BLOCK at 3+ |
| Noisy | Multi-run agreement < 60% | Excluded from verdict |
| Dropped | Parse error or missing dimension | Excluded from verdict |

Suite-level verdicts:

- **SHIP** — ≥1 improvement, 0 regressions
- **REVIEW** — 1–2 soft regressions, human decides
- **BLOCK** — any hard regression, or 3+ soft
- **NEUTRAL** — no change either way

That's the entire decision surface. No reading transcripts. No vibes.

## Cost Discipline

Eval is expensive. A full multi-run pass across all 12 scenarios costs ~$2–3 in API calls. Run that on every prompt change and you light money on fire.

Caching cuts ~80% of per-run cost. Anthropic prompt caching on the ~4K-token system prompt saves ~60% on input-token cost. A baseline-log cache keyed by a hash of each golden's simulation-relevant fields skips re-simulating the baseline entirely (~50% additional) until a golden changes.

Tiered evaluation, fail-fast:

| Tier | Cost | Runs | Stop rule |
|---|---|---|---|
| 1 | ~$0.35 | 2–3 targeted scenarios, 1 judge pass | Stop on first failure |
| 2 | ~$1.25 | All 12, 1 judge pass | Stop on first hard regression |
| 3 | ~$2–3 | All 12, 2-pass judging | Only if Tier 2 says SHIP / REVIEW |

A bad prompt change fails at Tier 1 for $0.35 instead of consuming the full $3. CI engineers do this for compute. Almost no one does it for evals.

## Versioning and Rollback

`src/core/prompt_versions/` contains 10 prompt files (v4 → v13, sequentially numbered). One rule: never overwrite a working prompt. Every change starts a new file. Same principle as a database migration — an in-place ALTER on production state is malpractice.

The honest part of the version log is the rollbacks.

Heavy gate frameworks — explicit if/else branches in the system prompt for state transitions, hard-coded refusal patterns for edge cases — got tried and rolled back. Beautiful in theory; in practice, the structural rewrites consistently underperformed lightweight behavioral patches against a known-good baseline. The harness caught it. Patches shipped instead.

**Subtract before you add.** The default instinct on a regression is to add a constraint, a guard rail, a structural rewrite. The harness data pointed the other way: most quality wins came from removing instructions that conflicted with each other, not adding new ones. The lesson applies to prompts as much as code — but you only learn it if you have a harness to tell you the heavy framework lost.

## What This Work Demands

Three habits this kind of work forces, none of which feel natural:

1. **Distrusting your own outputs by default.** The instinct after a prompt change is to read the outputs and decide if they're better. The control experiment shows why that's broken — the same prompt against itself looks like a regression 75% of the time. Human judgment on conversational outputs is high-variance, including mine.

2. **Treating API spend like compute.** CI engineers tier their pipelines without thinking about it — fast cheap checks first, expensive ones last. The same logic applies to evals, and skipping it burns money on every change. Not sophisticated; just borrowed from places where the discipline already exists.

3. **Keeping rollbacks visible.** Ten prompt versions where several were rollbacks is a more honest artifact than ten that all "worked." The rolled-back versions are where the harness earned its keep — proof that the structural rewrites that looked best on paper underperformed against baseline.

Eval-driven prompt work is closer to scientific method than software craft. Hypothesis → controlled comparison → verdict → ship or learn. The harness exists to keep the work honest with itself.

---

*Built solo, alongside the rest of a production AI application — auth, payments, persistence, deployment. The eval system is the part most worth writing about, because it's the part that revealed which prompt instincts were wrong.*

*Disclosure boundary: no proprietary prompts, scenario contents, rubric text, or domain specifics. The numbers (LOC, costs, control-experiment results, version count) are real, cross-checked against the source and `eval/runs/*.json` before publishing.*
