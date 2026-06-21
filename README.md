# Dynamic Truck Dispatching Optimization: Deterministic vs. Stochastic SAA Models

![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)
![SimPy](https://img.shields.io/badge/Simulation-SimPy-orange.svg)
![PuLP](https://img.shields.io/badge/Optimization-PuLP-green.svg)
![PyMoo](https://img.shields.io/badge/Optimization-PyMoo-lightgrey.svg)

**Author:** Md Shariq Jamil  
**Organization:** SIMLAB, University of Kentucky (Summer 2026, Project 01)  

## 📌 Project Overview
This repository contains a comprehensive pipeline for simulating, analyzing, and optimizing truck-to-shovel dispatch assignments in an open-pit mining environment. The project bridges the gap between predictive machine learning and prescriptive operations research, culminating in a **Two-Stage Stochastic Optimization** model that evaluates realistic financial risk under equipment failure uncertainty.

The dispatch models are built upon the multi-objective transportation problem framework presented in *A multiple objective transportation problem approach to dynamic truck dispatching* (European Journal of Operational Research, 2019).

## 🗂️ Repository Structure
```text
├── 01_simulation.ipynb           # SimPy discrete-event simulation (baseline fleet behavior)
├── 02_ml_analysis.ipynb          # XGBoost predictive modeling of cycle times and bottleneck analysis
├── 03_deterministic_moo.ipynb    # Week 4: Deterministic MILP & Multi-Objective Pareto Front
├── 04_stochastic_moo.ipynb       # Week 5: Two-Stage Stochastic SAA (Financial Risk Model)
├── PROGRESS.md                   # Detailed weekly task breakdown and conceptual findings
├── README.md                     # Project overview and instructions
└── results/
    ├── sim_detailed.csv          # Raw event logs from SimPy
    ├── sim_summary.csv           # Aggregated fleet performance metrics
    ├── pareto_deterministic.png  # 3D plot of deterministic tradeoffs
    └── pareto_stochastic_financial.png # 3D plot of stochastic financial risk tradeoffs
```

## 🚀 Key Phases & Methodology

### Phase 1: Simulation & Predictive Analysis (Weeks 1-3)
* **Discrete-Event Simulation:** Modeled a fleet of 10 trucks (100t and 240t capacities) and 4 shovels using `SimPy`. Incorporated dynamic routing, load times, and stochastic breakdown profiles (MTBF = 120 hours with Triangular repair times).
* **Bottleneck Identification:** Established a baseline cycle time of 34.6 minutes, observing shovel queues backing up to 5 trucks.
* **Predictive Modeling:** Deployed an `XGBoost` model to predict cycle times. The drop in $R^2$ (to 0.19) upon introducing breakdowns mathematically proved that cycle times are dominated by maintenance-state noise, necessitating a shift from predictive to prescriptive modeling.

### Phase 2: Deterministic Optimization (Week 4)
* Formulated the dispatch assignment as a Mixed-Integer Linear Programming (MILP) transportation problem using `PuLP`.
* Utilized `pymoo` (NSGA-II algorithm) to generate a 3D Pareto frontier trading off three competing operational objectives: **Haulage Cost vs. Expected Throughput vs. Equipment Wear**.

### Phase 3: Stochastic Financial Optimization (Week 5)
* Upgraded the baseline MILP to a **Two-Stage Stochastic Program** using Sample Average Approximation (SAA) with $N=50$ Monte Carlo scenarios.
* **Financial Enterprise Model:** Translated arbitrary mathematical penalties into a unified USD financial risk metric:
  * Variable OPEX: $4.16/min (100t trucks) and $7.50/min (240t trucks).
  * Stochastic Grade Volatility: $\mu = 1.5\%$ Cu target, $\sigma = 0.2\%$.
  * Market Recourse Penalty: $8,000/ton penalty for expected copper shortfalls.
* **Core Finding:** The stochastic frontier visually proved that schedules maximizing theoretical throughput under deterministic assumptions are highly fragile. Under stochastic maintenance conditions, these "efficient" schedules trigger massive expected market penalties, highlighting the profound Value of the Stochastic Solution (VSS).

## ⚙️ How to Run
1. Clone the repository:
   ```bash
   git clone [https://github.com/SIMLAB-UKY/simlab-summer2026-p01-jamil-truck-dispatch.git](https://github.com/SIMLAB-UKY/simlab-summer2026-p01-jamil-truck-dispatch.git)
   cd simlab-summer2026-p01-jamil-truck-dispatch
   ```
2. Install the required dependencies:
   ```bash
   pip install pandas numpy matplotlib simpy xgboost pulp pymoo
   ```
3. Run the Jupyter notebooks in sequential order, starting from `01_simulation.ipynb` to generate the foundational datasets, ending with `04_stochastic_moo.ipynb` for the final enterprise risk model.

## 📄 License
This project is for educational and research purposes under the SIMLAB University of Kentucky summer program.
