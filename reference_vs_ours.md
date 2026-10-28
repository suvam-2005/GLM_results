# Reference vs Ours — GLM

**There is no REFERENCE_RESULT for this model.** The ChildAgentEval paper (arXiv:2605.17894)
evaluated GPT-5.4, Gemini-3.1-Pro, Gemini-3.1-Flash-Lite, Qwen-3.6-Plus, Qwen-3.5-27B and
Gemma-4-31B, and no GLM model. Nothing in this document is a `REPRODUCTION_RESULT`.

This file is kept, under the same name as in the other result sets, so they line up.

## What the reference paper reported (other models, context only)

**REFERENCE_RESULT**, for other models: Qwen3.5-27B 0.32 / 0.34 / 0.29 / 0.30 (ages 7 / 10 / 13 /
16, medium confidence) and Gemma-4-31B 0.28 / 0.29 / 0.27 / 0.25 (low confidence). Shown in Figure
1 / fig07. These say nothing about GLM.

## What our L0 produced, alongside our other models

**OUR_RESULT.** L0 accuracy by age on the same benchmark and scoring:

| age | GLM 5.3 Flash | Qwen3.5-27B | Gemma 4 26B A4B | DeepSeek-V3.2 |
|---:|---:|---:|---:|---:|
| 7 | 0.750 (6/8) | 0.625 | 0.625 | 0.625 |
| 10 | 0.750 (6/8) | 0.500 | 0.500 | 0.500 |
| 13 | 0.750 (6/8) | 0.500 | 0.625 | 0.625 |
| 16 | 0.750 (6/8) | 0.625 | 0.625 | 0.625 |
| overall | 24/32 | 18/32 | 19/32 | 19/32 |

These are on the same scale, with one important caveat: **GLM reasoned before answering (it
can't be switched off) and the other three didn't.** GLM's lead is real for this setup, but
it mixes model capability with being allowed to think.

## What changed under L1/L2/L3/L4

**OUR_RESULT.** Accuracy: L1 24/32, L2 24/32, L3 22/32, L4 23/32. None of these changes was
caused by a guardrail (`benchmark_report.md` §13). L4 cut steps from 152 to 67 (−55.9%) and
cost from $0.024118 to $0.010620 (−56.0%).

## Statistical evidence

Paired comparison, n=32: L0 vs L1 and L2 0.000 (p=1.00); L0 vs L3 −0.063 (p=0.58); L0 vs L4
−0.031 (p=0.78). Bonferroni-adjusted p = 1.0 in all four.

## Limitations

- No reference value exists for GLM, so it can't be placed on the paper's scale.
- The cross-model table is one run per model, and the reasoning setting differs (above).
- No claim of reproduction, validation or invalidation of any ChildAgentEval result is made.
