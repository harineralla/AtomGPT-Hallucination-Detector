# AtomGPT Hallucination Detector: NeurIPS 2025 Benchmark Evaluation

Evaluation of the [AtomGPT reference checker](https://atomgpt.org/hallucination_detector) on 100 confirmed hallucinated citations from the GPTZero NeurIPS 2025 benchmark. Supporting code and data for the paper "Hallucination Detector: A Hybrid LLM and Semantic Scholar Tool for Detecting Hallucination in Scientific Literature on AtomGPT.org."

## What the tool does

Each citation is submitted to the AtomGPT reference checker, which extracts the bibliographic fields with an open-weight LLM (gpt-oss-20b), retrieves the closest real papers from Semantic Scholar, and scores agreement on title (60%), author (25%), and journal (15%). A composite score is thresholded into verified (>=80), partial (60-79), or likely hallucinated (<60).

## Files

| File | Description |
|------|-------------|
| `DetectorTool_Report_main.ipynb` | Analysis notebook (loads results, produces all figures) |
| `hallucination_report_2.csv` | Raw per-citation output exported from the AtomGPT UI |
| `ui_all_cols_results.csv` | Cleaned results table used in the analysis |
| `ui_status_distribution.png` | Status distribution (66/28/6) |
| `ui_component_by_status.png` | Mean component score by status |
| `ui_title_match_dist.png` | Title score distribution |
| `ui_author_match_dist.png` | Author score distribution |
| `ui_journal_match_dist.png` | Journal score distribution |

## Run

```
pip install pandas numpy matplotlib
jupyter notebook DetectorTool_Report_main.ipynb
```

Place `hallucination_report_2.csv` in the same directory, then run all cells.

## Data source

The 100 hallucinated citations are from the publicly released [GPTZero NeurIPS 2025 investigation](https://gptzero.me/news/neurips/).

## Key result

94 of 100 known fabrications flagged (66 likely hallucinated, 28 partial), 6 missed. Detection is driven primarily by author mismatch: fabricated citations often carry plausible titles but authors matching no real paper.
