# GLM 5.3 Flash — Guardrail Study Results (L0-L4)

Results for `z-ai/glm-5.3-flash` on the Independent Interactive Cognitive-Age Benchmark
(NOT ChildAgentEval). This folder has the same layout as
[Deepseek_results](https://github.com/suvam-2005/Deepseek_results) and
[Gema_results](https://github.com/suvam-2005/Gema_results): all five conditions, L0-L4, are
analysed and plotted together.

**Two differences from the other models:**

- **Reasoning could not be turned off.** GLM 5.3 Flash's endpoint rejects disabling it, so this
  run used the lowest setting (`effort: minimal`). GLM still reasoned on ~89% of steps, while
  Qwen, Gemma and DeepSeek ran with reasoning off. Reasoning tokens are measured for every call
  and reported (`benchmark_report.md` §21, `tables.md` Tables 10-11, and a third panel in
  `figures/figure10_tokens_cost.png`).
- **The reference paper has no GLM result.** As in `Deepseek_results`, the reference figures
  show the paper's other open-weight baselines (context only), and `figure2_l0_across_models.png`
  / `fig08_l0_across_models.png` compare GLM's L0 with our own Qwen, Gemma and DeepSeek runs.

| File | Contents |
|---|---|
| `outcome_brief.html` / `.md` | Plain-language, 12-page brief (open the HTML in a browser) |
| `benchmark_report.md` | Full technical report |
| `final_results_summary.md` | One-page summary, including a four-model comparison |
| `tables.md` | All result tables, including reasoning-token detail |
| `reference_vs_ours.md` | Why there is no reference comparison, and the cross-model one used instead |
| `figures/` | 10 research figures + `captions.json` |
| `outcome_figures/` | 10 presentation figures used by the brief |
| `outcome_data_summary.json` | Every headline number, machine-readable |
| `reproducibility_manifest.json` | Experiment IDs, git commit, config hash, timestamps |

Raw and processed data, trajectories, per-call usage logs and the scripts that produced this
folder live in the [ProjectCLG](https://github.com/suvam-2005/ProjectCLG) repository
(`configs/glm_independent_benchmark.yaml`, `scripts/analyze_glm_benchmark.py`,
`scripts/generate_glm_figures.py`, `scripts/build_glm_summary.py`).
