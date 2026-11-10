# InfraGuard AI

**Predictive analytics & early-warning system for MoSPI's PAIMANA infrastructure project portfolio.**

Built for Smart India Hackathon 2026, Problem Statement 26103 ("AI-Powered Predictive Analytics & Early-Warning System for Infrastructure Project Monitoring") — Team Vortex.

MoSPI's Infrastructure & Project Monitoring Division tracks ~1,900+ central infrastructure projects worth ₹150 crore and above through the PAIMANA portal. InfraGuard AI turns that monthly reporting data into forward-looking signal: which projects are likely to slip, by how much, and why — before the overrun is formally declared.

## What it does

- **Cost & schedule escalation risk model** (XGBoost) — ranks every ongoing project by the probability it declares a cost or schedule escalation in the next ~3 months, with plain-language reason codes per project.
- **Final-cost and end-date forecasting** — a hazard/survival model plus benchmarking against historical outcomes, giving an expected final cost and a predicted completion date with uncertainty bands, not just a point estimate.
- **Early-warning watchlist** — a filterable, sector/state/ministry-sliceable dashboard of at-risk projects.
- **GIS map** — geographic distribution of the portfolio color-coded by risk tier.
- **Project Intelligence Assistant** — an LLM chat (Groq, `gpt-oss-120b`) grounded only in the computed forecast data; it narrates numbers the pipeline already produced and is designed to never invent a project, cost, or date.
- **Benchmarking & model-accuracy pages** — sector/state comparisons and backtested accuracy of the underlying models, so the numbers are auditable, not a black box.

## Tech stack

| Layer | Tools |
|---|---|
| Data pipeline | pandas, custom PDF-table extraction from PAIMANA monthly Flash Reports |
| Modeling | scikit-learn, XGBoost, a rule-based hazard/survival model |
| Assistant | Groq API (`gpt-oss-120b`), grounded retrieval — no fine-tuning, no hallucinated numbers |
| Dashboard | Streamlit, Altair, Folium / streamlit-folium |
| Tests | Streamlit `AppTest` harness + plain unittest-style checks |

## Project structure

```
InfraGuard-AI/
├── app/                    # Streamlit dashboard
│   ├── Home.py              # Portfolio Overview (entry point)
│   ├── pages/                # 1_Early_Warning, 2_Project_Detail, 3_Models,
│   │                          # 4_Benchmarking, 5_GIS_Map, 6_Assistant
│   ├── assistant.py           # Grounded LLM assistant (Groq)
│   ├── data_loader.py         # Cached data loading + GIS aliasing
│   ├── risk_engine.py         # Risk-tier thresholds/colors
│   └── components/            # Shared navbar, filters, KPI cards, styling
├── src/                    # Model pipeline (v2, active)
│   ├── newdata.py             # Raw monthly export parsing + cleaning
│   ├── panel_v2.py             # Feature engineering / training panel
│   ├── train_v2.py             # Trains the XGBoost escalation model
│   ├── score_v2.py             # Scores the live roster, generates reason codes
│   ├── hazard.py               # Survival/hazard model for completion timing
│   ├── outcomes.py             # Realized cost/delay benchmark cohort
│   ├── forecast.py             # Final cost + end-date forecasting
│   ├── backtest.py             # Accuracy backtesting
│   └── test_v2.py              # Pipeline regression tests
├── scripts/                 # One-off PAIMANA live-portal investigation scripts
├── data/
│   ├── newdata/                # Monthly PAIMANA exports (extracted from source PDFs)
│   └── gold/                   # Cleaned training panels
├── models/                  # Trained model artifacts (.joblib)
├── reports/                 # Generated forecasts, backtests, MODEL_CARD.md
└── requirements.txt
```

## Getting started

### Prerequisites
- Python 3.11+
- A free [Groq API key](https://console.groq.com/keys) (optional — the assistant falls back to a templated answer without one; the rest of the dashboard works fully offline)

### Setup

```bash
git clone https://github.com/Sarvesh-107/InfraGuard-AI.git
cd InfraGuard-AI
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

Add your Groq key (optional, enables the Assistant page):

```bash
mkdir -p app/.streamlit
cat > app/.streamlit/secrets.toml <<EOF
GROQ_API_KEY = "your-key-here"
EOF
```

This file is gitignored — never commit it.

### Run the dashboard

```bash
streamlit run app/Home.py
```

### Refresh the models (optional)

To retrain against new monthly data dropped into `data/newdata/`, run the pipeline in order:

```bash
python src/panel_v2.py
python src/train_v2.py
python src/score_v2.py
python src/hazard.py
python src/forecast.py
python src/backtest.py
python src/test_v2.py   # verify nothing broke
```

### Run tests

```bash
python app/test_navbar.py
python app/test_assistant.py
python src/test_v2.py
```

## Data source & methodology

Training data comes from MoSPI PAIMANA's monthly Flash Reports (public PDF archive), extracted into structured CSVs. See `reports/MODEL_CARD.md` for the full methodology: data-quality fixes applied to the raw exports, model accuracy figures (backtested on unseen/completed projects), and known limitations.

**Current training window:** Jul 2024 – Jun 2025 (~14 months). A further year of extracted monthly data (through Jul 2026) exists and is queued for a future retrain to extend this window.

## Known limitations

- GIS map coordinates are illustrative (jittered within state boundaries), not surveyed project locations — the live PAIMANA source has no usable lat/lon join key for this.
- The "Ministry" filter currently mirrors the sector field — no reliable per-project ministry field was found in the available source data.
- Forecasts more than ~1 year out are explicitly flagged as low-confidence extrapolation in the underlying data.

## Contributing

This is a hackathon prototype built by Team Vortex. Pull requests and issues are welcome — please open an issue before a large change so we can discuss approach first.

## Team

Team Vortex — Smart India Hackathon 2026, PS 26103.
