# Benchmark Report — Independent Interactive Cognitive-Age Benchmark, L0-L4, GLM 5.3 Flash

**Model:** `z-ai/glm-5.3-flash` via OpenRouter. **Conditions:** L0, L1, L2, L3, L4, all run
and analysed together, with the same design as the Gemma and DeepSeek studies. **This is NOT
ChildAgentEval** (§4), and **the reference paper did not evaluate any GLM model** (§3).

> **Important difference from the other three models.** GLM 5.3 Flash cannot be run with
> reasoning disabled: its endpoint rejects `reasoning: {enabled: false}` with "Reasoning is
> mandatory for this endpoint and cannot be disabled". This run therefore used the lowest
> available setting, `effort: minimal`. Even at that setting, **GLM reasoned on 89% of steps
> (about 105 reasoning tokens per step on average)**, while Qwen3.5-27B, Gemma 4 26B A4B and
> DeepSeek-V3.2 all ran with reasoning off. Cross-model comparisons in this report should be read
> as "a model that reasons vs models that don't", not as like-for-like.

Every number here is read from `data/glm_benchmark/processed/` and
`data/glm_benchmark/statistics/` (`scripts/analyze_glm_benchmark.py`), and every figure from
`scripts/generate_glm_figures.py`. Run date: 2026-10-28. Config:
`configs/glm_independent_benchmark.yaml`.

## 1. Abstract

GLM 5.3 Flash was run through the same 32-task benchmark and five guardrail conditions (one
run each, temperature 0). It scored **24/32 at L0**, the highest of the four models tested
(Qwen 18, Gemma 19, DeepSeek 19). It answered all 24 multiple-choice tasks correctly and
failed all 8 planning/working-memory sequence tasks. L1 and L2 matched L0 exactly on every
task. L3 scored 22/32 and L4 23/32. **None of these drops was caused by a guardrail.** The
L4 loss came from a task where L4 never intervened. In both L3 losses, the model received
exactly the same input as under L0 at every step and simply gave a different final click. All
three losses fall on the only two tasks where GLM already switches answers mid-episode
under L0. As for every other model, L4 cut steps by 55.9% and cost by 56.0% relative to L0.
No comparison was significant (all p ≥ 0.58; Bonferroni-adjusted p = 1.0). Total spend:
$0.1083.

## 2. Research question

Do external guardrails (L1 output, L2 action, L3/L4 trajectory-level) change the task
accuracy or cognitive-age alignment of an interactive browser agent relative to an
unguarded control (L0), and does the pattern seen in Qwen, Gemma and DeepSeek hold for a
fourth model, one that reasons before acting?

## 3. Reference study

*Evaluating Cognitive Age Alignment in Interactive AI Agents* (arXiv:2605.17894,
ChildAgentEval). The paper evaluated GPT-5.4, Gemini-3.1-Pro, Gemini-3.1-Flash-Lite,
Qwen-3.6-Plus, Qwen-3.5-27B and Gemma-4-31B, and **no GLM model**. As in the DeepSeek report,
Figure 1 / fig07 show the paper's two open-weight baselines as context (other models), and
Figure 2 / fig08 compare GLM's L0 with **our own** Qwen, Gemma and DeepSeek L0 runs on this
benchmark (§14).

## 4. Why an independent benchmark was necessary

Unchanged from the Qwen study: the official ChildAgentEval repository contains no tasks,
scoring code or data (`docs/phase0_findings.md`).

## 5. Benchmark methodology

Identical to the other runs: real headless Chromium (Playwright), DOM-assisted observations,
generic click/type/select/scroll/keypress/wait/finish actions, binary scoring done after the
episode against `success_condition` (never shown to the agent), and the project's own linear
age-alignment formula.

## 6. Task design

The same 32 original tasks. SHA-256 of the task files in sorted path order:
`31ccc0b035de3fde4483f1ff6609e5de3a59293d7f908e47ba8122c4f323748c`, identical to the Gemma and
DeepSeek runs.

## 7. Model configuration

| field | value |
|---|---|
| model | `z-ai/glm-5.3-flash` (not `flashx`, not `:batch`) |
| provider | OpenRouter |
| temperature | 0 |
| max_output_tokens | 256 (same as the other models) |
| response_format | `json_object` |
| reasoning | **`effort: minimal`**. Cannot be disabled on this endpoint; the other three models used `enabled: false` |
| per-step prompt | identical to the other models |
| pricing (live, run date) | $0.15 / M input tokens, $0.50 / M output tokens |

Two code changes were needed, both additive with the other runs' behaviour unchanged (297
tests pass):
- `run_independent_benchmark.py` accepts an `extra_request_params` override from the
  config. Its default is still reasoning disabled.
- `OpenRouterClient` can log each call's usage breakdown, including reasoning tokens, to
  `data/glm_benchmark/raw/<condition>_usage.jsonl`.

**Choosing this setting.** A single probe call at `effort: minimal` reported 0 reasoning
tokens, but the 2-task smoke test showed reasoning on 10 of 11 steps. The decision to proceed
with the same 256-token limit, and flag the reasoning, was made after seeing the smoke test.

## 8. Experimental conditions

| condition | guardrail | version | what it does |
|---|---|---|---|
| L0 | none | — | control |
| L1 | output | L1-v1 | repairs/rejects malformed JSON output |
| L2 | action | L2-v1 | rejects finish-at-step-0 and targets not on the page |
| L3 | dynamic | L3-v1 | repeat → substitute `wait`; ≥5 steps → force `finish` |
| L4 | dynamic | L4-v2 | ≥5 steps → force `finish`; repeat → force `finish` immediately |

## 9. Budget

GLM spend, all five conditions: **$0.108325**. That includes a 2-task L0 smoke test ($0.002145)
charged to the GLM L0 ledger, and is 1.4% of the $8.00 GLM benchmark cap. Whole-project
lifetime spend after this run: $0.436980 over 3,027 API calls (of the $10.00 hard cap).

## 10. L0 results (no guardrail)

24/32 correct (75.0%). Alignment 0.575. 152 steps, 119,420 tokens, $0.024118. Zero errors.
Reasoning on 136 of 152 steps, 15,996 reasoning tokens in total.

## 11. L1 results (output guardrail)

24/32 (75.0%). Alignment 0.575. **0 interventions.** Identical per-task outcomes to L0 on
all 32 tasks. $0.024324.

## 12. L2 results (action guardrail)

24/32 (75.0%). Alignment 0.575. **0 interventions.** Identical per-task outcomes to L0 on
all 32 tasks. $0.024431.

## 13. L3 and L4 results (trajectory guardrails)

**L3:** 22/32 (68.75%), alignment 0.55625. 69 interventions (62 repeated-action → `wait`,
7 excessive-steps → `finish`; 48.3% of 143 steps). 143 steps (−5.9%), $0.022686 (−5.9%). It
differs from L0 on two tasks, both correct → incorrect:

| task | L0 clicks | L3 clicks | correct |
|---|---|---|---|
| `integrated_age10_v1` | b c b b | b c c→wait c | b |
| `visual_spatial_age13_v1` | a d a d | d d→wait d a | d |

L3 intervened once on each. **The model's input at every step was identical to its input
under L0**: the same page and the same element list. A `wait` substitution leaves the page
unchanged, and the prompt has no action history. The wrong final click was therefore
generated from the same input that produced the right one under L0. It's model variability,
not an L3 effect.

**L4:** 23/32 (71.875%), alignment 0.571875. 31 interventions, all repeated-action →
immediate `finish` (46.3% of 67 steps). 67 steps (−55.9% vs L0, −53.2% vs L3), 52,246 tokens
(−53.3% vs L3), $0.010620 (−56.0% vs L0, −53.2% vs L3). It differs from L0 on one task,
`integrated_age10_v1` (correct → incorrect), where **L4 did not intervene at all**: the model
clicked `b c b c` and ended on the wrong answer by itself.

**These are the only two tasks where GLM switches answers within a single L0 episode.**
Their outcome is decided by whichever answer happens to come last, so they're the tasks
where any rerun, guarded or not, can flip.

## 14. L0 across the four models (in place of a reference comparison)

All columns are **our** results on this benchmark. GLM reasoned; the others did not.

| age | GLM 5.3 Flash | Qwen3.5-27B | Gemma 4 26B A4B | DeepSeek-V3.2 |
|---:|---:|---:|---:|---:|
| 7 | 0.750 | 0.625 | 0.625 | 0.625 |
| 10 | 0.750 | 0.500 | 0.500 | 0.500 |
| 13 | 0.750 | 0.500 | 0.625 | 0.625 |
| 16 | 0.750 | 0.625 | 0.625 | 0.625 |
| overall | **24/32** | 18/32 | 19/32 | 19/32 |

(Gemma's and DeepSeek's lines coincide exactly in fig08.)

## 15. Alignment analysis

Alignment: L0, L1, L2 0.575; L3 0.55625; L4 0.571875 (Figure 3). GLM has the highest
alignment of the four models at L0 (Qwen 0.519, Gemma 0.534, DeepSeek 0.534), a direct
consequence of its higher accuracy under the project's rising target curve. Within GLM, the
L3/L4 drops are the task-level changes explained in §13.

## 16. Capability analysis

Accuracy: L0-L2 0.75, L3 0.6875, L4 0.71875 (Figure 4; SEM ≈ 0.078-0.083, n=32).

## 17. Alignment-capability tradeoff

Capability retention vs L0: L1 1.000, L2 1.000, L3 0.917, L4 0.958 (Figure 5). L0, L1 and L2
coincide exactly. The L3/L4 points fall below L0 for the variability reasons in §13, not
because of a guardrail-induced tradeoff.

## 18. Per-age analysis

| condition | age 7 | age 10 | age 13 | age 16 |
|---|---:|---:|---:|---:|
| L0 | 6 | 6 | 6 | 6 |
| L1 | 6 | 6 | 6 | 6 |
| L2 | 6 | 6 | 6 | 6 |
| L3 | 6 | 5 | 5 | 6 |
| L4 | 6 | 5 | 6 | 6 |

Under L0-L2, GLM scores exactly 6/8 at every age: all six multiple-choice tasks at each age
right, both sequence tasks wrong. That's a flat profile with no age trend, which is a
limitation of the alignment metric (its target rises with age) rather than of the model.

## 19. Per-category analysis

| category | L0 | L1 | L2 | L3 | L4 | Qwen L0 | Gemma L0 | DeepSeek L0 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| abstraction | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 |
| integrated_reasoning | 1.00 | 1.00 | 1.00 | 0.75 | 0.75 | 1.00 | 1.00 | 0.25 |
| language | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 |
| planning | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 |
| processing_behavior | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 |
| reasoning | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 | 0.00 | 0.50 | 0.75 |
| visual_spatial | 1.00 | 1.00 | 1.00 | 0.75 | 1.00 | 0.50 | 0.25 | 0.75 |
| working_memory | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 |

**GLM is the only model with perfect reasoning and visual-spatial scores.** Those are the
two categories involving a short calculation (number patterns, rotations). **[INTERPRETATION]**
The most plausible explanation is the reasoning GLM does before answering, which the other
three models were not allowed to do. This run can't separate "GLM is a stronger model"
from "GLM was allowed to think".

**Planning and working memory: 0% in every condition, as for all three other models.**
Reasoning didn't help: GLM, like the others, clicked the first required element and then
re-clicked it on every remaining step (e.g. `wm_age10_v1`: `target_square` × 7). Four models,
including one that reasons, now fail these tasks in the identical way. That's strong evidence
the cause is the harness (no action history in the prompt, no visible record of clicks on the
page), not the models' ability.

## 20. Intervention analysis

| condition | interventions | rate | triggers |
|---|---:|---:|---|
| L0 | 0 | 0.0% | — |
| L1 | 0 | 0.0% | — |
| L2 | 0 | 0.0% | — |
| L3 | 69 | 48.3% | repeated_action 62, excessive_steps 7 |
| L4 | 31 | 46.3% | repeated_action 31 |

In line with the other models (L3: 64-70; L4: 30-31).

## 21. Reasoning tokens, errors and run-to-run variability

**Reasoning** (`processed/reasoning.json`, from the per-call usage logs; every call accounted
for):

| condition | calls | calls with reasoning | reasoning tokens | mean per call | calls at the 256 limit | reasoning share of output |
|---|---:|---:|---:|---:|---:|---:|
| L0 | 152 | 136 (89%) | 15,996 | 105.2 | 40 | 90.2% |
| L1 | 152 | 134 (88%) | 16,414 | 108.0 | 42 | 90.6% |
| L2 | 152 | 136 (89%) | 16,616 | 109.3 | 41 | 90.6% |
| L3 | 143 | 129 (90%) | 15,228 | 106.5 | 40 | 90.5% |
| L4 | 67 | 61 (91%) | 7,226 | 107.9 | 19 | 90.9% |

About 90% of GLM's output tokens are reasoning, and the visible answer is the remaining ~10%.
Roughly a quarter of calls report a completion of the full 256 tokens. Despite that, every
call ended with `finish_reason: stop` and **every step returned a valid action. There were 0
malformed outputs and 0 recoveries in every condition**, so the token limit never actually
cut off an answer in this run. Why the provider reports 256 completion tokens alongside a
complete answer isn't clear from the data. It's recorded here as observed.

**Variability.** L0, L1 and L2 (all zero interventions) produced identical outcomes on all 32
tasks, so GLM's outcomes were stable across behaviourally identical runs, like Qwen's. Within
episodes, however, GLM switched answers on the same two tasks in L0 (`integrated_age10_v1`,
`visual_spatial_age13_v1`), and those two tasks account for every change seen under L3 and L4
(§13).

## 22. Token/cost analysis

| condition | input tokens | output tokens | API calls | cost (USD) | Δ cost vs L0 |
|---|---:|---:|---:|---:|---:|
| L0 | 101,690 | 17,730 | 152 | $0.024118 | — |
| L1 | 101,744 | 18,125 | 152 | $0.024324 | +0.9% |
| L2 | 101,744 | 18,339 | 152 | $0.024431 | +1.3% |
| L3 | 95,139 | 16,831 | 143 | $0.022686 | −5.9% |
| L4 | 44,294 | 7,952 | 67 | $0.010620 | −56.0% |

GLM's output is about 8× the other models' (17,730 vs ~2,200 tokens at L0) because of
reasoning. Even so, its L0 run ($0.024118) cost less than DeepSeek's ($0.029830), about the
same as Qwen's ($0.023440), and more than Gemma's ($0.008856). L1/L2's small cost increases
reflect slightly more reasoning, not interventions. Latency was ~2.4 s per step.

## 23. Statistical analysis

| comparison | mean diff | 95% CI | discordant | Welch p | Bonferroni p |
|---|---:|---|---:|---:|---:|
| L0 vs L1 | 0.000 | [0.000, 0.000] | 0 | 1.000 | 1.000 |
| L0 vs L2 | 0.000 | [0.000, 0.000] | 0 | 1.000 | 1.000 |
| L0 vs L3 | −0.063 | [−0.148, 0.023] | 2 | 0.583 | 1.000 |
| L0 vs L4 | −0.031 | [−0.092, 0.030] | 1 | 0.780 | 1.000 |
| L3 vs L4 | +0.031 | [−0.030, 0.092] | 1 | 0.788 | — |

No comparison is significant before or after correction. An exact McNemar test on 2
same-direction discordant pairs gives p = 0.5.

## 24. Limitations

- **Reasoning confound**: GLM reasoned and the other models didn't, so cross-model
  differences mix model capability with being allowed to think.
- One run per condition; 32 tasks (4 per category, 8 per age).
- L1 and L2 never fired: their effect is untested, not disproven.
- The benchmark is original and not psychometrically validated. Planning/working-memory
  scores are dominated by the stateless prompt (§19).
- No reference result exists for GLM.

## 25. Threats to validity

- **Persona prompt**: GLM answered every multiple-choice task correctly even at the
  age-7 targets, so it didn't "act its age". **[INTERPRETATION]** Reasoning may override the
  role-play instruction, in contrast to DeepSeek's childlike errors.
- **Last-click scoring × answer switching**: on GLM's two answer-switching tasks, the
  outcome depends on the final sampled click (§13).
- **Token-limit reporting**: calls reporting 256 completion tokens with a complete answer
  (§21) are unexplained. If a future run hits the limit for real, raising `max_output_tokens`
  would be needed for GLM.

## 26. Reproducibility

```bash
python scripts/run_independent_benchmark.py --config configs/glm_independent_benchmark.yaml --condition L0   # then L1..L4
python scripts/analyze_glm_benchmark.py
python scripts/generate_glm_figures.py
python scripts/build_glm_summary.py --tests-passed 297 --tests-total 297
```

`reproducibility_manifest.json` lists experiment IDs, git commit, config hash and timestamps.
Per-step trajectories: `data/glm_benchmark/raw/trajectories/{L0..L4}/`; per-call usage
(reasoning tokens): `data/glm_benchmark/raw/{L0..L4}_usage.jsonl`. Test suite: 297/297
passed.

## 27. Conclusion

Four models, same guardrail result. On GLM 5.3 Flash, no guardrail from L1 to L4 caused an
outcome change; the three task-level differences trace to model variability on two unstable
tasks, and L4 again cut steps and cost by more than half. GLM is the most accurate of the
four (24/32), almost certainly helped by reasoning that the other models didn't do. Yet it
fails the planning and working-memory tasks exactly like the others. Reasoning doesn't fix
what the harness withholds, which is the agent's own action history.
