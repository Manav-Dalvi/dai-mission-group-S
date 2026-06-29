# Beyond Raw Pace: Strategic Decision-Making in Formula 1

**DAI Mission | Data and AI in Economics**
**TU Dortmund | Summer Semester 2026**
**Supervisors:** Prof. Dr. Peter N. Posch, Kira Sophie Schönhütte

---

## Research Question

To what extent do strategic decision-making under uncertainty and resource constraint management
contribute to competitive success in Formula 1 beyond car performance, and what factors determine
the size of these strategic gains?

We investigate this question across four analytical blocks using data spanning 2003-2024 (8,966
driver-race observations). The study is framed through the lens of decision-making under resource
constraints: pit timing under safety car windows, mandatory grid penalty placement, and the
interaction between car quality and strategic returns.

---

## Team and Block Ownership

| Member | Block | Method |
|---|---|---|
| Sasha Marie Stühmer | Block 1 — Unsupervised Learning | PCA + K-Means circuit clustering |
| Manav Madhukar Dalvi | Block 2 — Causal Inference A | Two-Stage Least Squares (2SLS IV) |
| Manav Madhukar Dalvi | Block 3 — Causal Inference B | DoWhy backdoor adjustment |
| Manav Madhukar Dalvi | Data pipeline, synthesis, project lead | |
| Bademba Drammeh | Block 4 — Supervised Learning | Walk-forward XGBoost + SHAP |

Block ordering in the notebook follows analytical dependency, not convention:
Unsupervised (1) → Causal A (2) → Causal B (3) → Supervised (4).
Block 1 circuit clusters are consumed as moderators in Blocks 2 and 3, and as features in Block 4.

---

## Repository Structure

```
dai-mission-group-S/
├── README.md
├── requirements.txt
├── Beyond_Raw_Pace.ipynb           # Main submission notebook (fully executed)
├── data_extraction_pipeline/
│   ├── data_extraction_pipeline.ipynb  # Reference only -- see note below 
├── data_dictionary.csv
├── data/
│   ├── master_driver_race.csv      # Primary analytical table (8,966 rows x 48 cols)
│   ├── circuit_cluster.csv         # Block 1 output: circuit names + cluster labels
│   ├── penalty_cost_elasticity.csv # Block 3 output: per-circuit penalty cost
│   ├── code_to_driverid.json
│   ├── b4_m4_predictions.csv       # Block 4 output: M4 walk-forward predictions
│   ├── strategy_features.csv 
│   └── fastf1_laps.csv           
└── outputs
│   └── plots/     
└── slides/
    └── beyond_raw_pace_slides.pdf
```

---

## How to Run

### Option 1: Local 

```bash
git clone https://github.com/Manav-Dalvi/dai-mission-group-S.git
cd dai-mission-group-S
pip install -r requirements.txt
jupyter notebook Beyond_Raw_Pace.ipynb
```

Run all cells top to bottom. No external API calls or environment configuration required.
All data is read from the `data/` folder in the repository root.

### Option 2: Google Colab

Upload the repository folder to Google Drive, then open `Beyond_Raw_Pace.ipynb` in Colab.
The notebook will auto-detect the Colab environment and mount Drive if needed.
No path configuration is necessary.

---

## Data

All data required to reproduce the analysis is provided in the `data/` folder.

| File | Rows | Description |
|---|---|---|
| `master_driver_race.csv` | 8,966 | Primary analytical table. One row per driver per race, 2003-2024. Contains race results, qualifying gaps, pit stop counts, safety car flags, tyre compounds (2018+), weather, and constructed strategy features. |
| `circuit_cluster.csv` | 22 | Circuit typology from Block 1. Cluster label per circuit (22 of 27 circuits retained after coverage threshold). |
| `penalty_cost_elasticity.csv` | varies | Per-circuit penalty cost elasticity (OLS slope) from Block 3. Used as a moderator in the strategic timing test. |
| `b4_m4_predictions.csv` | varies | Walk-forward out-of-sample predictions from Block 4 Model M4. Used in the synthesis section. |

**Data sources (for reference):**
- Race results, qualifying, pit stops, standings: Jolpica API (successor to Ergast), accessed May-June 2026.
- Lap-level telemetry and tyre compound data: FastF1 Python library v3.x, scoped to 2018-2024.
- Race-day precipitation: Open-Meteo Historical Weather API, accessed May 2026.

---

## Note on the Extraction Pipeline

`data_extraction_pipeline.ipynb` is included for transparency and reproducibility of the data
collection process. **Graders and reviewers should not run this notebook.**

Reasons:
- It makes live API calls to Jolpica and Open-Meteo and requires network access.
- The FastF1 portion requires approximately 6-8 hours of runtime due to the 500 calls/hour
  rate limit and session-level checkpointing across 2018-2024.
- All outputs from this pipeline are already provided in the `data/` folder.

The extraction notebook is annotated throughout and documents all query patterns, rate limit
handling, and known data gaps (19 FastF1 sessions missing: 2018 R14 and 2023 R5-R22).

---

## Known Data Limitations

- **FastF1 coverage gaps:** 19 sessions could not be retrieved due to API availability.
  Tyre compound and safety car lap-level features are therefore missing for these races.
  All downstream analyses using these features are restricted to rows with non-null values
  and this scope restriction is documented in the notebook.
- **Tyre compound boundary:** Compound data is only used for races from 2018 onward,
  when Pirelli's current compound naming scheme was introduced.
- **Circuit coverage:** 22 of 27 circuits met the minimum observation threshold for
  cluster assignment. The 5 excluded circuits are documented in Block 1.

---
