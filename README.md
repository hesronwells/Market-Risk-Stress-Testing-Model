# Market-Risk-Stress-Testing-Model
I built a risk engine for a $1.7M portfolio using MySQL, Python, and Power BI. MySQL stores prices and stocks. Python pulls Yahoo Finance data, runs VaR/CVaR models, and calculates 2008, COVID, and Rate Hike shocks. Power BI visualizes live P&amp;L and sector impacts. It’s an end-to-end stack for institutional-grade stress testing and risk transparency
A end-to-end market risk stress testing model built across three tools — MySQL, Python, and Power BI. The model simulates how a diversified $1.7M equity portfolio performs under three stress scenarios: the 2008 Financial Crisis, the COVID Crash of 2020, and a hypothetical Rate Hike of +200 basis points.

Demo

VisualDescriptionKPI CardsDaily VaR (95%), CVaR (95%), Max DrawdownScenario Bar ChartTotal stressed P&L per scenarioSector BreakdownP&L contribution by sector per scenarioPortfolio DonutAllocation across 5 sectorsScenario SlicerToggle between all 3 scenarios interactively

Architecture
Yahoo Finance (yfinance)
        │
        ▼
        
┌─────────────────┐

│   MySQL 8.0     │  stress_test_db

│  - portfolio    │  20 stocks, weights, sectors

│  - price_history│  2 years of daily returns

│  - scenarios    │  shock % per ticker per scenario

│  - stress_results│ VaR, CVaR, P&L output

└─────────────────┘

        │
        ▼
        
┌─────────────────┐

│  Python 3.9     │  Jupyter Notebook

│  - yfinance     │  pulls real price data

│  - risk engine  │  VaR, CVaR, Max Drawdown

│  - writes back  │  results → MySQL

└─────────────────┘

        │
        ▼
        
┌─────────────────┐

│   Power BI      │  Interactive Dashboard

│  - star schema  │  scenarios as hub table

│  - DAX measures │  Stressed P&L, VaR, CVaR

│  - slicer       │  toggle scenarios live

└─────────────────┘



Portfolio
20 stocks across 5 sectors — total value $1,700,000
SectorStocksAllocationTechnologyAAPL, MSFT, NVDA, GOOGL, META25%FinancialsJPM, GS, BAC, MS, BLK20%EnergyXOM, CVX, COP, SLB15%HealthcareJNJ, PFE, UNH, ABBV15%ConsumerAMZN, TSLA10%


Stress Scenarios
ScenarioTypeMethod2008 Financial CrisisHistoricalPeak-to-trough returns Sep–Nov 2008 calculated from Yahoo FinanceCOVID Crash 2020HistoricalPeak-to-trough returns Feb–Mar 2020 calculated from Yahoo FinanceRate Hike +200bpsHypotheticalInterest rate beta analysis per sector
Historical shocks are calculated programmatically from real price data — not hardcoded. The hypothetical scenario uses interest rate beta analysis: growth stocks carry the highest negative beta, banks benefit from net interest margin expansion, and energy acts as an inflation hedge.


Risk Metrics
Calculated using Historical Simulation method on 2 years of daily returns:
MetricDefinitionVaR 95%Maximum expected daily loss on 95% of daysCVaR 95%Average loss on the worst 5% of daysMax DrawdownLargest peak-to-trough decline over 2 years


Project Structure
stress-test-model/
│
├── README.md
├── sql/
│   ├── 01_schema.sql
│   ├── 02_portfolio.sql
│   ├── 03_scenarios.sql
│   └── 04_sector_view.sql
│
├── python/
│   └── stress_test.ipynb
│
└── charts/
    ├── chart1_pnl_distribution.png
    ├── chart2_cumulative_drawdown.png
    └── chart3_scenario_comparison.png


How to Run
Prerequisites


MySQL 8.0
Python 3.9 with: yfinance, pymysql, pandas, numpy, scipy, matplotlib, seaborn
Power BI Desktop with MySQL Connector/NET 8.0.40


Steps


Run SQL scripts in order: 01_schema.sql → 02_portfolio.sql → 03_scenarios.sql → 04_sector_view.sql
Open stress_test.ipynb in Jupyter and update DB_CONFIG with your MySQL password
Run all cells top to bottom
Open Power BI, connect to localhost:3306 / stress_test_db, load all tables and the view
Build the dashboard or open the included .pbix file



Key Design Decisions


Historical shocks from real data — Python calculates actual crisis returns from Yahoo Finance rather than using hardcoded estimates
Peak-to-trough methodology — captures worst-case drawdown within the crisis window, more conservative than simple period returns
Sector average imputation — META, ABBV and TSLA had no 2008 data (pre-IPO). Missing shocks were filled using sector peer averages — standard practice in risk modelling
MySQL view for sector P&L — computation pushed to the database layer so Power BI stays clean and both tools read from the same source
Historical Simulation over Parametric VaR — makes no normality assumption, naturally captures fat tails in equity return distributions


Tools & Libraries
LayerToolPurposeDatabaseMySQL 8.0Schema, storage, viewsData pullyfinanceReal historical price dataComputationPython / pandas / numpyRisk engineVisualisation (dev)matplotlib / seabornValidation chartsDashboardPower BI DesktopInteractive presentationDB connectorpymysqlPython → MySQL connection
