# AtomGPT Hallucination Detector: Benchmark Evaluation

Evaluation of the [AtomGPT reference checker](https://atomgpt.org/hallucination_detector) on fabricated and genuine scientific citations. Supporting code and data for the paper *"Hallucination Detector: A Hybrid LLM and Semantic Scholar Tool for Detecting Hallucination in Scientific Literature on AtomGPT.org."*

The tool is benchmarked on three datasets: the GPTZero **NeurIPS 2025** set (100 confirmed hallucinations), the **CiteAudit** benchmark (balanced genuine + fabricated), and a **materials-science pilot**. The verification pipeline source lives in a separate repository: [atomgptlab/hallucination_detector](https://github.com/atomgptlab/hallucination_detector).

## What the tool does

Each citation is submitted to the AtomGPT reference checker, which extracts the bibliographic fields with an open-weight LLM (gpt-oss-20b, with gemma-4-26b as an automatic fallback since July 2026), retrieves the closest real papers from Semantic Scholar, and scores agreement on title (60%), author (25%), and journal (15%). The composite score is thresholded into **verified** (>=80), **partial** (60-79), or **likely hallucinated** (<60). The extraction uses deterministic (greedy) decoding and the scoring is deterministic, so results are reproducible for a fixed model and retrieval.

## Key results

| Benchmark | Composition | Result |
|-----------|-------------|--------|
| NeurIPS 2025 | 100 confirmed hallucinations | 94/100 flagged (66 likely hallucinated, 28 partial, 6 missed), recall 0.94 |
| CiteAudit | 299 scored (149 genuine, 150 fabricated) | precision 0.80, recall 0.49, false-positive rate 0.12, accuracy 0.69 |
| Materials pilot | 34 citations (18 genuine, 16 fabricated) | genuine and fabricated fully separated on this small set |

Detection is driven primarily by **author mismatch**: fabricated citations often carry plausible titles but authors matching no real paper. On CiteAudit the misses are mostly subtle corruptions that leave the author list intact, and a reference-type analysis shows that bare data and software citations (material IDs, dataset DOIs, software releases) cannot be verified against a literature index.

## Files

**Notebook**

| File | Description |
|------|-------------|
| `DetectorAPI_Report_main.ipynb` | Runs all three benchmarks through the AtomGPT API and produces the figures |

**NeurIPS 2025 benchmark**

| File | Description |
|------|-------------|
| `hallucination_report_2.csv` | Raw per-citation output exported from the AtomGPT UI |
| `ui_all_cols_results.csv` | Cleaned NeurIPS results table used in the analysis |
| `ui_status_distribution.png` | Status distribution (66/28/6) |
| `ui_component_panels.pdf` | Three-panel per-field score distribution (paper figure) |
| `ui_component_by_status.png` | Mean component score by status |
| `ui_title_match_dist.png` | Title score distribution |
| `ui_author_match_dist.png` | Author score distribution |
| `ui_journal_match_dist.png` | Journal score distribution |

**CiteAudit benchmark**

| File | Description |
|------|-------------|
| `citeaudit_all300_combined.csv` | Merged results with per-field scores and status |
| `atomgpt_citeaudit_300.png` | CiteAudit status distribution |

**Materials-science pilot**

| File | Description |
|------|-------------|
| `materials_citations.json` | Genuine and fabricated materials citations by reference type |
| `materials_test_results.csv` | Materials pilot results by reference type |
| `materials_by_category.png` | Materials status by reference type |

**Methods figures**

| File | Description |
|------|-------------|
| `atomgpt_flowchart.png` | Evaluation pipeline diagram |
| `tool_snapshot_2.png` | Screenshot of the AtomGPT web interface |

## Run

```bash
pip install requests pandas numpy matplotlib
jupyter notebook DetectorAPI_Report_main.ipynb
```

The notebook needs an AtomGPT key. Set it as an environment variable before launching (it is never hardcoded):

```bash
export AGAPI_KEY="your-key-here"
```

## Data sources

- The 100 hallucinated citations are from the publicly released [GPTZero NeurIPS 2025 investigation](https://gptzero.me/news/neurips/).
- CiteAudit is from [Shi et al., 2026](https://arxiv.org/abs/2602.23452) ([repository](https://github.com/shiiiikw/CiteAudit)).
