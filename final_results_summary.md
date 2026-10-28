# Final Results Summary — GLM 5.3 Flash

Independent Interactive Cognitive-Age Benchmark (NOT ChildAgentEval), `z-ai/glm-5.3-flash`,
conditions L0-L4, one run each, temperature 0. Full detail: `benchmark_report.md`.

> **Reasoning could not be turned off for this model** (the endpoint rejects it). It ran at the
> lowest setting, `effort: minimal`, and still reasoned on ~89% of steps (~105 reasoning tokens
> per step). The other three models ran with reasoning off.

## Reference results

**None.** The reference paper (arXiv:2605.17894) did not evaluate any GLM model.

## Results by condition

| Condition | Correct | Accuracy | Alignment | Interventions | Steps | Tokens | Reasoning tokens | Cost |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| L0 (no guardrail) | 24/32 | 75.00% | 0.575000 | 0 | 152 | 119,420 | 15,996 | $0.024118 |
| L1 (output) | 24/32 | 75.00% | 0.575000 | 0 | 152 | 119,869 | 16,414 | $0.024324 |
| L2 (action) | 24/32 | 75.00% | 0.575000 | 0 | 152 | 120,083 | 16,616 | $0.024431 |
| L3 (dynamic, wait on repeat) | 22/32 | 68.75% | 0.556250 | 69 (48.3%) | 143 | 111,970 | 15,228 | $0.022686 |
| L4 (dynamic, finish on repeat) | 23/32 | 71.88% | 0.571875 | 31 (46.3%) | 67 | 52,246 | 7,226 | $0.010620 |

## Accuracy and alignment changes

None attributable to a guardrail. L1 and L2 matched L0 on all 32 tasks. L4's one lost task had
**no L4 intervention**. L3's two lost tasks had one intervention each, but the model's input was
identical to L0's at every step, so the different final click was model variability. All three
losses fall on the two tasks where GLM already switches answers mid-episode under L0.

## Efficiency

L3: −5.9% steps and cost vs L0. **L4: −55.9% steps, −56.0% cost vs L0; −53.2% steps and cost
vs L3.**

## Cost

GLM benchmark spend: **$0.108325** for all five conditions, including a $0.002145 two-task
smoke test (1.4% of the $8.00 cap). Project lifetime spend: $0.436980 over 3,027 API calls.

## Statistical evidence

Paired, n=32 tasks: L0 vs L1 and L2 0.000 (p=1.00); L0 vs L3 −0.063 (p=0.58); L0 vs L4 −0.031
(p=0.78); L3 vs L4 +0.031 (p=0.79). Bonferroni-adjusted p = 1.0 for all. No effect detected.

## Four models compared (same benchmark, same conditions)

| | Qwen3.5-27B | Gemma 4 26B A4B | DeepSeek-V3.2 | GLM 5.3 Flash |
|---|---|---|---|---|
| Reasoning | off | off | off | **on (minimal; can't disable)** |
| L0 accuracy | 18/32 | 19/32 | 19/32 | **24/32** |
| Accuracy range across L0-L4 | 18-18 | 17-20 | 19-20 | 22-24 |
| Tasks differing between unguarded runs (L0/L1/L2) | 0 | 3 | 1 | 0 |
| Planning / working memory | 0% / 0% | 0% / 0% | 0% / 0% | 0% / 0% |
| Reasoning category (L0) | 0% | 50% | 75% | 100% |
| Integrated reasoning (L0) | 100% | 100% | 25% | 100% |
| Visual-spatial (L0) | 50% | 25% | 75% | 100% |
| Malformed outputs | 1-2 per condition | 0 | 0 | 0 |
| L4 steps vs L3 | −53.8% | −49.7% | −53.2% | −53.2% |
| L0 cost | $0.023440 | $0.008856 | $0.029830 | $0.024118 |

**Across all four models:** no guardrail changed accuracy beyond model variability, L4
consistently halves the steps and cost, and planning and working memory are 0% everywhere,
including for the one model that reasons.

## Limitations

Reasoning confound in cross-model comparisons; single run per condition; 32 tasks; L1/L2
untested because they never fired; original, unvalidated benchmark; no reference result for
GLM. Full list: `benchmark_report.md` §24-25.
