# Publication Tables — GLM 5.3 Flash, L0-L4

All values are read from `data/glm_benchmark/processed/` and `data/glm_benchmark/statistics/`,
none hand-typed. All five conditions are in one table set.

## Table 1 — Model and configuration

| model | provider | temperature | max_output_tokens | response_format | reasoning |
|---|---|---:|---:|---|---|
| z-ai/glm-5.3-flash | openrouter | 0 | 256 | json_object | **effort: minimal** (cannot be disabled; other models: disabled) |

## Table 2 — Reference methodology vs this run

Official ChildAgentEval access remains unavailable; this run used the Independent
Interactive Cognitive-Age Benchmark, with identical tasks and harness to the other runs. The
only setting that differs is reasoning (Table 1).

## Table 3 — Reference results

**No reference result exists**: the paper did not evaluate any GLM model. In its place, L0
accuracy by age for the four models run on this benchmark (all OUR results):

| age | GLM 5.3 Flash | Qwen3.5-27B | Gemma 4 26B A4B | DeepSeek-V3.2 |
|---:|---:|---:|---:|---:|
| 7 | 0.750 | 0.625 | 0.625 | 0.625 |
| 10 | 0.750 | 0.500 | 0.500 | 0.500 |
| 13 | 0.750 | 0.500 | 0.625 | 0.625 |
| 16 | 0.750 | 0.625 | 0.625 | 0.625 |

## Table 4 — Independent-benchmark results (L0-L4)

| condition | n | n_correct | mean_accuracy | alignment | identical outcomes to L0 |
|---|---:|---:|---:|---:|---:|
| L0 | 32 | 24 | 0.75000 | 0.575000 | 32/32 |
| L1 | 32 | 24 | 0.75000 | 0.575000 | 32/32 |
| L2 | 32 | 24 | 0.75000 | 0.575000 | 32/32 |
| L3 | 32 | 22 | 0.68750 | 0.556250 | 30/32 |
| L4 | 32 | 23 | 0.71875 | 0.571875 | 31/32 |

## Table 5 — Paired comparisons (same 32 tasks)

| comparison | task_performance_diff | discordant | incorrect→correct | correct→incorrect |
|---|---:|---:|---|---|
| L0 vs L1 | 0.00000 | 0 | — | — |
| L0 vs L2 | 0.00000 | 0 | — | — |
| L0 vs L3 | −0.06250 | 2 | — | integrated_age10_v1, visual_spatial_age13_v1 |
| L0 vs L4 | −0.03125 | 1 | — | integrated_age10_v1 |
| L3 vs L4 | +0.03125 | 1 | visual_spatial_age13_v1 | — |

For the L4 change, L4 did not intervene on the task. For the two L3 changes, the model's
per-step input was identical to L0's (`benchmark_report.md` §13).

## Table 6 — Statistical significance / effect sizes

| comparison | n | mean_diff 95% CI | welch_t | p_value | bonferroni_p | cohens_d_paired |
|---|---:|---|---:|---:|---:|---:|
| L0 vs L1 | 32 | [0.000, 0.000] | 0.000 | 1.000 | 1.000 | undefined (zero variance) |
| L0 vs L2 | 32 | [0.000, 0.000] | 0.000 | 1.000 | 1.000 | undefined (zero variance) |
| L0 vs L3 | 32 | [−0.148, 0.023] | −0.549 | 0.583 | 1.000 | −0.254 |
| L0 vs L4 | 32 | [−0.092, 0.030] | −0.279 | 0.780 | 1.000 | −0.177 |
| L3 vs L4 | 32 | [−0.030, 0.092] | 0.269 | 0.788 | — | 0.177 |

## Table 7 — Per-age accuracy (correct of 8)

| condition | age 7 | age 10 | age 13 | age 16 |
|---|---:|---:|---:|---:|
| L0 | 6 | 6 | 6 | 6 |
| L1 | 6 | 6 | 6 | 6 |
| L2 | 6 | 6 | 6 | 6 |
| L3 | 6 | 5 | 5 | 6 |
| L4 | 6 | 5 | 6 | 6 |

## Table 8 — Per-category accuracy (n=4 each)

| category | L0 | L1 | L2 | L3 | L4 |
|---|---:|---:|---:|---:|---:|
| abstraction | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 |
| integrated_reasoning | 1.00 | 1.00 | 1.00 | 0.75 | 0.75 |
| language | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 |
| planning | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 |
| processing_behavior | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 |
| reasoning | 1.00 | 1.00 | 1.00 | 1.00 | 1.00 |
| visual_spatial | 1.00 | 1.00 | 1.00 | 0.75 | 1.00 |
| working_memory | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 |

## Table 9 — Interventions

| condition | interventions | steps | rate | repeated_action | excessive_steps |
|---|---:|---:|---:|---:|---:|
| L0 | 0 | 152 | 0.000 | 0 | 0 |
| L1 | 0 | 152 | 0.000 | 0 | 0 |
| L2 | 0 | 152 | 0.000 | 0 | 0 |
| L3 | 69 | 143 | 0.483 | 62 | 7 |
| L4 | 31 | 67 | 0.463 | 31 | 0 |

## Table 10 — Token/cost analysis

| condition | input_tokens | output_tokens | reasoning_tokens | api_calls | cost_usd | errors | recoveries |
|---|---:|---:|---:|---:|---:|---:|---:|
| L0 | 101,690 | 17,730 | 15,996 | 152 | $0.024118 | 0 | 0 |
| L1 | 101,744 | 18,125 | 16,414 | 152 | $0.024324 | 0 | 0 |
| L2 | 101,744 | 18,339 | 16,616 | 152 | $0.024431 | 0 | 0 |
| L3 | 95,139 | 16,831 | 15,228 | 143 | $0.022686 | 0 | 0 |
| L4 | 44,294 | 7,952 | 7,226 | 67 | $0.010620 | 0 | 0 |

Reasoning tokens are included in output tokens.

## Table 11 — Reasoning detail

| condition | calls with reasoning | mean reasoning tokens / call | max | calls reporting 256 output tokens | finish_reason |
|---|---:|---:|---:|---:|---|
| L0 | 136/152 | 105.2 | 256 | 40 | stop × 152 |
| L1 | 134/152 | 108.0 | 256 | 42 | stop × 152 |
| L2 | 136/152 | 109.3 | 256 | 41 | stop × 152 |
| L3 | 129/143 | 106.5 | 256 | 40 | stop × 143 |
| L4 | 61/67 | 107.9 | 256 | 19 | stop × 67 |

## Table 12 — Failure analysis

Not populated: the per-step failure taxonomy wasn't run, as in the other models' tables.
The dominant failure modes are described in `benchmark_report.md` §19 and §21.
