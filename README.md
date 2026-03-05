# Market Volatility Sentinel: Python ETL Pipeline

A lightweight, automated ETL (Extract, Transform, Load) pipeline that simulates a modern Data Lake architecture. This project tracks the correlation between the **USD/EGP exchange rate** and **Gold Futures (GC=F)** to automatically detect and flag high-volatility market events.

## 🏗️ Project Architecture

This pipeline is broken down into three distinct phases:

### Phase 1: Multi-Source Extraction (The Data Lake)
* Extracts live USD/EGP exchange rates via the `ExchangeRate-API`.
* Extracts a 5-day historical trend of Gold Futures using the `yfinance` library.
* **The Engineering Win:** Raw data is saved as `.json` files in a timestamped `/raw_ingestion/[date]/` directory, preserving the exact state of the original data before any transformations occur.

### Phase 2: The Transformation Engine
* Ingests the raw JSON data using `pandas`.
* Merges the disparate datasets on the `Date` index using an SQL-style Inner Join.
* Calculates daily percentage changes to build a **Volatility Index**.
* **The Logic:** If the Egyptian Pound drops in value by > 1% *and* Gold prices rise simultaneously, the day is flagged as "HIGH VOLATILITY".
* Outputs a clean, structured `daily_market_pulse.csv`.

### Phase 3: The Sentinel Alert System & Visualization
* Reads the transformed CSV and evaluates the latest market data.
* If the Volatility Index is triggered, it automatically generates:
  1. A **Dual-Axis Chart (`matplotlib`)** showing the visual correlation between Gold and USD/EGP.
  2. An **Executive Summary (`.txt`)** detailing the percentage changes and market analysis.
* **The Level-Up:** Utilizes Python's native `logging` library to maintain a persistent `sentinel_pipeline.log` audit trail.

---

## ⚙️ Installation & Setup

1. **Clone the repository:**
   ```bash
   git clone [https://github.com/yourusername/market-volatility-sentinel.git](https://github.com/yourusername/market-volatility-sentinel.git)
   cd market-volatility-sentinel
Install the required dependencies:
This project relies on a few external libraries. Install them via pip:

Bash
pip install requests yfinance pandas matplotlib
🚀 How to Run the Pipeline
Run the scripts in sequential order to process the data from raw ingestion to final alerting:

Step 1: Extract the Data

Bash
python phase1_ingestion.py
(Check your folder tree—you will see a new raw_ingestion/ folder organized by today's date).

Step 2: Transform the Data

Bash
python phase2_transformation.py
(This generates the daily_market_pulse.csv file).

Step 3: Run the Sentinel Alerts

Bash
python phase3_sentinel.py
(If volatility is detected, check your directory for the generated .png chart, .txt summary, and .log file).

📂 Expected Directory Structure
After running all three phases, your project folder will look like this:

Plaintext
market-volatility-sentinel/
│
├── phase1_ingestion.py
├── phase2_transformation.py
├── phase3_sentinel.py
│
├── raw_ingestion/
│   └── YYYY-MM-DD/
│       ├── exchange_rates.json
│       └── gold_prices.json
│
├── daily_market_pulse.csv
├── sentinel_pipeline.log
├── volatility_alert_chart_YYYY-MM-DD.png
└── Executive_Summary_YYYY-MM-DD.txt
📝 Note on Phase 2 "Reality Check"
Because this pipeline relies on daily percentage changes, Day 1 execution includes a mocked "yesterday" value for the USD/EGP rate to demonstrate the alerting logic. For ongoing production use, remove the mock block in phase2_transformation.py and load the previous day's JSON file.
