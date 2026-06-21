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

## Week 5: Two-Stage Stochastic Optimization
- [x] Integrate MTBF Exp(120) and Triangular repair distributions into capacity projections.
- [x] Generate $N=50$ Monte Carlo breakdown scenarios for Sample Average Approximation (SAA).
- [x] Solve Two-Stage MILP baseline explicitly accounting for second-stage unmet demand penalties.
- [x] Transition PyMoo constraints to soft recourse penalties to prevent infeasibility under high-demand stress (1,650t).
- [x] Export `pareto_stochastic.png` visualizing the robust tradeoff frontier.

**Key Finding (Week 5):** When the production target (1,650t) squeezed the stochastic capacity limits of the fleet, the deterministic "cheapest" schedules collapsed. The Two-Stage SAA successfully mapped a new Pareto frontier spanning massive composite costs (0 to 300,000+), mathematically isolating brittle schedules and proving that true FMS optimization requires scheduling buffer capacity to avoid catastrophic second-stage shortfalls.