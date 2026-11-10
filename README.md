
# InfraGuard AI

**Predictive Analytics and Early-Warning System for Infrastructure Project Monitoring**

InfraGuard AI is a machine learning-based infrastructure monitoring platform designed to analyse MoSPI's PAIMANA project data and predict potential cost overruns and schedule delays.

The platform transforms monthly infrastructure project reports into actionable insights by identifying projects at risk, estimating final project costs, forecasting completion dates, and providing explanations for potential delays.

MoSPI's Infrastructure and Project Monitoring Division tracks approximately 1,900+ central infrastructure projects worth ₹150 crore and above through the PAIMANA portal. InfraGuard AI uses this reporting data to provide early warnings and support data-driven project monitoring.

## Features

- **Cost and Schedule Risk Prediction:** Uses an XGBoost model to identify projects likely to experience cost or schedule escalation within approximately the next three months.
- **Risk Explanation:** Generates project-level reason codes to explain the factors contributing to predicted risks.
- **Final Cost Forecasting:** Estimates expected final project costs using historical project outcomes and forecasting models.
- **Completion Date Prediction:** Applies a hazard/survival-based model to estimate project completion dates with uncertainty information.
- **Early-Warning Dashboard:** Provides a filterable watchlist of high-risk infrastructure projects with sector and state-based analysis.
- **GIS Map:** Visualises the geographical distribution of projects based on their predicted risk categories.
- **Project Intelligence Assistant:** Integrates a Groq-powered LLM assistant to answer questions using computed project forecast data.
- **Model Benchmarking:** Provides sector-wise and state-wise comparisons along with backtested model performance and accuracy information.

## Technology Stack

| Component | Technologies |
|---|---|
| Programming Language | Python |
| Data Processing | Pandas, NumPy |
| Machine Learning | Scikit-learn, XGBoost |
| Forecasting | Hazard/Survival Model, Historical Benchmarking |
| Data Extraction | PDF Table Extraction, PAIMANA Flash Reports |
| Dashboard | Streamlit |
| Data Visualization | Altair, Folium, Streamlit-Folium |
| Generative AI | Groq API, gpt-oss-120b |
| Testing | Streamlit AppTest, Python unittest |
| Version Control | Git, GitHub |

## Project Architecture

```text
InfraGuard-AI/
│
├── app/
│   ├── Home.py
│   ├── pages/
│   │   ├── 1_Early_Warning
│   │   ├── 2_Project_Detail
│   │   ├── 3_Models
│   │   ├── 4_Benchmarking
│   │   ├── 5_GIS_Map
│   │   └── 6_Assistant
│   │
│   ├── assistant.py
│   ├── data_loader.py
│   ├── risk_engine.py
│   └── components/
│
├── src/
│   ├── newdata.py
│   ├── panel_v2.py
│   ├── train_v2.py
│   ├── score_v2.py
│   ├── hazard.py
│   ├── outcomes.py
│   ├── forecast.py
│   ├── backtest.py
│   └── test_v2.py
│
├── scripts/
├── data/
│   ├── newdata/
│   └── gold/
│
├── models/
├── reports/
├── requirements.txt
└── README.md
```

## Getting Started

### Prerequisites

- Python 3.11 or later
- Git
- Groq API key (optional, required for the AI Assistant functionality)

### 1. Clone the Repository

```bash
git clone https://github.com/Jatin457/InfraGuard-AI.git
cd InfraGuard-AI
```

### 2. Create a Virtual Environment

```bash
python -m venv .venv
```

Activate the environment:

**Windows:**
```bash
.venv\Scripts\activate
```

**macOS/Linux:**
```bash
source .venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure the Groq API Key (Optional)

Create the Streamlit secrets directory:

```bash
mkdir -p app/.streamlit
```

Create a `secrets.toml` file inside that directory:

```toml
GROQ_API_KEY = "your-groq-api-key"
```

The API key is optional. Without it, the dashboard remains functional, while the assistant uses its fallback response.

**Important:** Never upload your API key or secrets file to GitHub.

### 5. Run the Application

```bash
streamlit run app/Home.py
```

The application will start locally and provide a URL in your terminal.

## Machine Learning Pipeline

The project includes a pipeline for processing PAIMANA data, training prediction models, generating forecasts, and evaluating model performance.

Run the scripts in the following order:

```bash
python src/panel_v2.py
python src/train_v2.py
python src/score_v2.py
python src/hazard.py
python src/forecast.py
python src/backtest.py
```

Run the pipeline regression tests:

```bash
python src/test_v2.py
```

## Testing

The project includes automated checks for different application components.

Run the following commands:

```bash
python app/test_navbar.py
python app/test_assistant.py
python src/test_v2.py
```

## Data Source and Methodology

The machine learning pipeline uses publicly available monthly Flash Reports from MoSPI's PAIMANA portal.

The reports are processed using custom PDF-table extraction and data-cleaning scripts to create structured datasets for model training and forecasting.

**Current training window:** July 2024 – June 2025 (approximately 14 months).

Additional extracted monthly data through July 2026 is available for future model retraining and evaluation.

For detailed information about data preparation, model methodology, backtesting results, and limitations, refer to:

`reports/MODEL_CARD.md`

## Known Limitations

- GIS map coordinates are illustrative and jittered within state boundaries because the available source data does not provide a reliable project-level latitude and longitude mapping.
- The Ministry filter currently uses sector information because reliable project-level ministry data was unavailable.
- Forecasts extending beyond approximately one year are marked as low-confidence extrapolations.
- Model predictions depend on the availability and quality of historical project reporting data.

## Future Improvements

- Incorporate additional historical PAIMANA reports to improve model training.
- Improve model performance through additional feature engineering and validation.
- Enhance project-level risk explanations and forecasting.
- Improve geographical accuracy through better project-location data.
- Expand automated testing and model monitoring.

## Contributing

Suggestions, improvements, and bug reports are welcome.

For major changes, please open an issue to discuss the proposed modifications before submitting a pull request.

## License

Refer to the repository's license file for usage and distribution terms.
