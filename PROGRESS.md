# SIMLAB Summer 2026: Project 01 Progress
**Intern:** Md Shariq Jamil

## Week 2: Simulation Pipeline
- [x] Build SimPy Discrete Event Simulation (10 trucks, 4 shovels, 2 dumps, 12-hr shift).
- [x] Implement stochastic breakdowns (Exponential MTBF 120hr) and Triangular repair times.
- [x] Execute 30 independent replications.
- [x] Export detailed cycle logs (`sim_detailed.csv`) for Week 3 ML feature engineering.
- [x] Calculate mean cycle times and 95% Confidence Intervals per replication (`sim_summary.csv`).
- [x] Generate production histogram and CI visualization plots.

**Key Finding (Week 2):** The simulation established a highly efficient baseline mean cycle time of 34.62 minutes, but identified significant system bottlenecks at the loading phase with maximum shovel queues reaching 5 trucks. This confirms the necessity of implementing a shortest-queue dispatcher in subsequent weeks.

## Week 3: Machine Learning Prediction
- [x] Load `sim_detailed.csv` as ML training data.
- [x] Engineer rolling queue, load time, and hour of shift features.
- [x] Train and compare Linear Regression, Random Forest, and XGBoost on 80% split.
- [x] Evaluate MAE, RMSE, and R² on held-out 20% test set.
- [x] Generate SHAP explainability plot.
- [x] Export `xgboost_cycle_model.pkl` to `models/` directory.

**Key Finding (Week 3):** Introducing realistic equipment breakdowns caused a significant drop in model performance (XGBoost R² dropped to 0.19, with Linear Regression slightly outperforming it at 0.24). This explicitly demonstrates that without feature-engineering the breakdown state, stochastic repair times appear as massive unpredictable noise, proving that machine learning in mining requires deep integration with maintenance schedules, not just operational telemetry.

## Week 4: Deterministic Multi-Objective Optimization
- [x] Set up Transportation Problem MILP parameters using `sim_detailed.csv` averages.
- [x] Formulate Cost, Throughput, and Wear objective equations.
- [x] Solve single-objective baseline utilizing the PuLP CBC solver (Achieved optimal cost of 129.00 mins).
- [x] Generate 3D Pareto frontier utilizing the `pymoo` NSGA-II algorithm.
- [x] Export `pareto_deterministic.png` showing trade-off surfaces.

**Key Finding (Week 4):** By relaxing strict shovel demands to accommodate discrete truck capacities, the deterministic MILP successfully identified a 129.00-minute baseline cost. The resulting 100-point Pareto frontier visually confirms the core premise of multi-objective dispatching: operational choices must sacrifice theoretical maximum throughput or cost efficiency to maintain acceptable equipment wear levels.

## Week 5: Two-Stage Stochastic Optimization (Financial Enterprise Model)
- [x] Integrate MTBF Exp(120) and Triangular repair distributions into capacity projections (dropping 100t expected capacity to 98.47t).
- [x] Generate $N=50$ Monte Carlo scenarios utilizing Sample Average Approximation (SAA).
- [x] Inject realistic variable OPEX ($4.16/min for 100t; $7.50/min for 240t) and stochastic copper grade volatility (1.5% target, 0.2% std dev).
- [x] Standardize objective into a unified USD Financial Risk metric incorporating an $8,000/ton market shortfall penalty.
- [x] Solve Two-Stage MILP baseline under high-demand stress (1,650t), yielding an expected financial baseline risk of $10,885.64 USD.
- [x] Evaluate multi-objective dispatch inside `pymoo` utilizing soft-recourse financial constraints.
- [x] Export `pareto_stochastic_financial.png` visualizing the realistic financial tradeoff frontier.

**Key Finding (Week 5):** By converting physical units (minutes and tons) into unified financial metrics (USD) and introducing ore grade volatility, the Two-Stage SAA successfully isolates enterprise risk. The stochastic Pareto frontier mathematically proves that pushing a fleet to its deterministic throughput limits under uncertain maintenance conditions causes expected market shortfall penalties to drastically outweigh any baseline operational savings, pushing financial risk well beyond the $10,885.64 USD optimized baseline.