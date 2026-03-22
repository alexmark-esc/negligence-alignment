# negligence-alignment

# Negligent AI: Reasonable Care for AI Safety — Data Repository

Data and scoring materials for "Negligent AI: Reasonable Care for AI Safety" (Mark, 2026).

## Repository Contents

- **master_all_models_results.xlsx** — Complete dataset of ~4,040 model responses (baseline + distribution) across 4 models × 5 scenarios × 2 conditions. Each model has its own sheet. A `permissiveness_score` column contains the 1–5 score assigned to each distribution response via the classifier pipeline described in Section 3.2 of the paper. Baseline responses are scored in the paper's Section 5.2 table and are not duplicated in this column.
- **scoring_sheets/** — Per-model worksheets documenting the representative samples used to calibrate the string-matching classifiers.
  - `claude_opus_scoring_sheet.xlsx`
  - `gpt52_scoring_sheet.xlsx`
  - `gemini_scoring_sheet.xlsx`
  - `grok4_scoring_sheet.xlsx`

## Column Dictionary (master file, per-model sheets)

| Column | Description |
|--------|-------------|
| A | `model_requested` — OpenRouter model string used in the API call |
| B | `model_actual` — Model string returned by the API in the response metadata |
| C | `data_type` — `baseline` (temperature=0, 1 run per cell) or `distribution` (temperature=1, 100 runs per cell) |
| D | `scenario` — One of: `football_throw`, `glass_pile`, `lightning_tree`, `roof_repair`, `trampoline` |
| E | `scenario_title` — Human-readable scenario name |
| F | `condition` — `c1_default` (open-ended prompt) or `c2_negligence` (negligence-framed prompt) |
| G | `run_number` — Run index (1 for baseline; 1–100 for distribution) |
| H | `temperature` — Sampling temperature (0.0 for baseline, 1.0 for distribution) |
| I | `prompt` — Full prompt text sent to the model |
| J | `response` — Full model response text |
| K | `timestamp` — ISO 8601 timestamp of the API response |
| L | `permissiveness_score` — Integer 1–5 assigned by the scoring pipeline (distribution responses only; blank for baselines and excluded responses) |

## Scoring Methodology

1. For each model×scenario×condition cell, Claude Opus 4.6 grouped the 100 distribution responses into structurally distinct clusters and produced 2–6 representative samples.
2. The author hand-scored each representative sample on a 1–5 permissiveness rubric (see paper, Section 3.1).
3. Opus 4.6 generated deterministic string-matching classifiers (Python functions) calibrated to the author's hand scores. These classifiers assign scores based on the presence or absence of specific textual features (e.g., keywords, structural markers), not LLM judgment.
4. Classifiers were iteratively debugged through spot-checking against the author's readings.
5. Each distribution response was scored individually by the classifier. The reported means are computed from these per-response scores.

## Reproducing Reported Means

For any cell, the reported mean equals the arithmetic mean of all non-blank values in the `permissiveness_score` column for that model, scenario, and condition, filtered to `data_type = distribution`.

## Excluded Responses

The following responses were excluded from scoring (permissiveness_score left blank):

- **Grok 4, Lightning C2**: 29 responses were safety-filter refusals. Grok characterized the negligence prompt as "an attempt to override or limit my responses to a narrow framework." (N=71 scored)
- **Grok 4, Football C2**: 8 responses returned empty or malformed. (N=92 scored)
- **Grok 4, Roof C2**: 1 response failed to return. (N=99 scored)
- **GPT-5.2, Glass C2**: 1 empty response. (N=99 scored)
- **Gemini 3.1 Pro, Glass C1**: 1 empty response. (N=99 scored)
- **Gemini 3.1 Pro, Trampoline C2**: 1 empty response. (N=99 scored)

All other cells contain N=100 scored distribution responses.

## API Details

- **API**: OpenRouter (https://openrouter.ai)
- **Model strings**: `anthropic/claude-opus-4-6`, `openai/gpt-5.2`, `google/gemini-3.1-pro-preview`, `x-ai/grok-4`
- **Data collection**: March 2026
- **Temperature**: 0.0 for baseline runs, 1.0 for distribution runs
- **Note on GPT-5.2**: OpenAI's GPT-5 family only accepts temperature=1. OpenRouter silently dropped the temperature=0 parameter for baseline runs, meaning GPT-5.2 baseline and distribution responses may have been generated under identical conditions.

## Citation

Mark, Alex. "Negligent AI: Reasonable Care for AI Safety." (2026). [paper URL]

## License

[Choose one — CC BY 4.0 is standard for research data]
