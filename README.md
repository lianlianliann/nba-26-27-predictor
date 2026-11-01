# 2026-27 NBA Championship Predictor

An end-to-end predictive sports analytics, machine learning, and 10,000-season Monte Carlo simulation pipeline forecasting regular season standings, postseason seedings, and championship outcomes for the **2026-27 NBA season**.

**Full Report & Interactive Web Portal:** [View the published report](https://lianlianliann.github.io/nba-26-27-predictor/)

---

## Objective

This project forecasts the complete 2026-27 NBA season through the 2027 NBA Finals. Rather than relying on simple win-loss records or subjective power rankings, the pipeline translates player-level telemetry, roster turnover, contextual fatigue, and team efficiency into causal pre-game win probabilities. 

These probabilities feed a **10,000-season Monte Carlo simulation engine** that plays out all 1,230 regular season games, the official NBA Play-In Tournament, and complete Best-of-7 playoff series to generate robust probability distributions for every team's title odds, conference seedings, and finals matchups.

---

## Key Findings & Championship Verdict

- **Championship Favorite (Denver Nuggets — 31.2% Title Probability):** Denver stands as the undisputed league frontrunner, projected for a league-best **58.1 wins** and a **45.5% Finals appearance rate**, propelled by Nikola Jokić's league-leading Bayesian rotation valuation.
- **Eastern Conference Frontrunner (New York Knicks — 20.6% Title Probability):** The Knicks lead the East with **55.4 projected wins** and a **38.4% Finals appearance rate**, establishing **NYK vs. DEN (17.7%)** as the single most frequent simulated NBA Finals pairing.
- **Top Contender Hierarchy:** Four franchises hold an aggregate **78.4% share** of all simulated championships: Denver (31.2%), New York (20.6%), Philadelphia (14.4%), and Oklahoma City (12.2%).
- **The Play-In Window:** The Eastern and Western Conference 7–10 seeds are separated by a razor-thin 39-to-47 win threshold, demonstrating the high variance and volatility of the play-in bubble.

### Top 10 Contender Projections

| Team | Conf | Projected Wins | Playoff Prob | Conf Finals Prob | Finals Prob | Championship Prob |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Denver Nuggets (DEN)** | West | **58.1** | 99.98% | 64.7% | 45.5% | **31.2%** |
| **New York Knicks (NYK)** | East | **55.4** | 99.91% | 58.3% | 38.4% | **20.6%** |
| **Philadelphia 76ers (PHI)** | East | **53.5** | 99.61% | 51.8% | 29.5% | **14.4%** |
| **Oklahoma City Thunder (OKC)**| West | **53.9** | 99.67% | 45.1% | 22.3% | **12.2%** |
| **San Antonio Spurs (SAS)** | West | **50.0** | 97.35% | 26.5% | 11.1% | **4.8%** |
| **Houston Rockets (HOU)** | West | **49.3** | 96.89% | 25.0% | 9.9% | **4.4%** |
| **Cleveland Cavaliers (CLE)** | East | **49.1** | 96.41% | 26.6% | 11.9% | **3.9%** |
| **Minnesota Timberwolves (MIN)**| West | **46.8** | 90.07% | 14.1% | 4.6% | **1.7%** |
| **Boston Celtics (BOS)** | East | **45.6** | 85.82% | 14.9% | 5.6% | **1.7%** |
| **Orlando Magic (ORL)** | East | **45.7** | 86.29% | 14.5% | 4.7% | **1.3%** |

*(See the [full published report](https://lianlianliann.github.io/nba-26-27-predictor/) for the complete 30-team projection table and seed distributions).*

---

## Power BI Executive Analytics Suite

The simulation telemetry and data model were compiled into a publication-grade, dark-themed Microsoft Power BI dashboard inspired by official NBA.com analytics portals. The interactive report file is tracked directly in [`powerbi/nba26-27.pbix`](powerbi/nba26-27.pbix).

### Page 1: Championship Overview & Title Shares
![Power BI Page 1: Overview & Championship Probabilities](assets/dashboard_page1_overview.png)

### Page 2: Matchups, Win Distributions & Postseason Seeds
![Power BI Page 2: Matchups & Postseason Simulation](assets/dashboard_page2_matchups.png)

---

## Dataset & Sources

- **NBA Official Stats API (`stats.nba.com` via `nba_api`):** 2,460 regular season game logs (2024–25 and 2025–26 seasons) and complete postseason player box scores.
- **Basketball-Reference:** Historical playoff series outcomes, franchise win totals, and award voting data.
- **NBA Transactions & Opening Week Rosters (October 2026):** Active 15-man rosters, depth charts, age, experience, and trade movements.

---

## Tools & Tech Stack

- **Python (Pandas, NumPy, SciPy):** Ingestion, feature pipelines, rolling window efficiency calculations, and Monte Carlo engines.
- **Scikit-Learn & LightGBM:** Pre-game win classification, chronological time-series splitting, calibration curves, and ROC-AUC validation.
- **Power BI Desktop & DAX:** Star-schema dimensional modeling (`dim_teams`, `fact_team_projections`, `fact_finals_matchups`, `fact_win_distribution`, `fact_seed_distribution`), custom dark stats theme, and conditional formatting heatmaps.
- **Quarto & GitHub Pages:** Reproducible technical publication, dark mode styling, and public web hosting.

---

## Feature Engineering Architecture

The predictive engine relies on an exhaustive nine-layer feature engineering framework:

1. **Dean Oliver's Four Factors:** Quantifying the fundamental pillars of basketball efficiency for offense and defense—Effective Field Goal Percentage (`eFG%`), Turnover Rate (`TOV%`), Offensive Rebounding Percentage (`OREB%`), and Free Throw Rate (`FTR`).
2. **Pace-Adjusted Advanced Ratings:** Normalizing scoring across differing game speeds to compute true Offensive Rating (`ORTG`), Defensive Rating (`DRTG`), and Net Rating (`NET_RATING = ORTG - DRTG`).
3. **Causal Rolling Pre-Game Form:** Calculating strictly backwards-looking 10-game rolling efficiency metrics and differential advantages (`DIFF_ROLL_NET_RATING`, `DIFF_ROLL_EFG`, etc.) knowable *prior* to tip-off, fully eliminating circular look-ahead data leakage.
4. **Contextual Schedule Fatigue & Rest:** Modeling rest days, back-to-back flags (`B2B`), and rest differentials (`REST_DIFF = Home Rest - Away Rest`) to capture physical fatigue.
5. **Bayesian VORP Shrinkage:** Applying empirical Bayes shrinkage ($K = 8.0$, replacement level $3.90$) across multi-season player Value Over Replacement Player (VORP) metrics to stabilize small-sample variance while preserving generational superstar impact.
6. **Rookie Draft-Slot Prior Imputation:** Solving the historical "rookie valuation dilemma" by calibrating historical draft-slot priors (Top-3 Picks: `6.60`, Lottery 4–14: `4.84`, Non-Lottery 1st Round: `2.80`, 2nd Round / Undrafted: `1.50`) so high-impact rookies are neither ignored nor artificially zeroed out.
7. **Rotation-Weighted Roster Aggregation:** Filtering each 15-man roster down to the core top-9 rotation players by minutes and value (`TOTAL_ROSTER_VALUE`), capturing the postseason reality that bench depth matters far less than playoff rotation strength.
8. **Playoff Experience & Resilience:** Quantifying roster-wide cumulative career playoff games played (`TOTAL_PLAYOFF_EXPERIENCE`) to capture crunch-time poise and championship battle-testing.
9. **Calibrated Modern Synergy Power Score:** Unifying team strength through a balanced 40/50/10 formula: 40% historical franchise rolling efficiency, 50% Bayesian active rotation value, and 10% playoff pedigree.

---

## Predictive Modeling Results

To prevent look-ahead bias, game features were computed strictly from historical pre-game performance (rolling 10-game efficiency metrics, rest advantage, and Bayesian roster valuation). Models were evaluated on an out-of-sample chronological test set:

| Model | Accuracy | ROC-AUC | Engineering Notes |
| :--- | :---: | :---: | :--- |
| **Logistic Regression (Baseline)** | **66.99%** | **0.7324** | **Best calibrated model.** Avoided overfitting, aligned with true NBA home/away margins, and produced optimal probabilities for simulation. |
| **Random Forest Classifier** | 66.42% | 0.7251 | Strong non-linear interaction capture; slightly less calibrated on high-probability favorites. |
| **XGBoost Classifier** | 62.60% | 0.6731 | Tendency to overfit on noise in small rolling game windows without aggressive regularization. |

---

## Project Structure

```
nba-26-27-predictor/
├── assets/                          # High-resolution dashboard screenshots & vector PDF
│   ├── dashboard_page1_overview.png
│   ├── dashboard_page2_matchups.png
│   └── nba26-27.pdf
├── data/                            # Raw box scores & processed feature snapshots
│   ├── raw/                         # Multi-season box scores, rosters & draft history
│   └── processed/                   # Cleaned games, rolling ratings & roster VORP snapshots
├── docs/                            # Compiled Quarto HTML website for GitHub Pages
│   ├── index.html                   # Executive landing page & contender summaries
│   ├── notebooks/                   # Static HTML versions of all 5 notebook chapters
│   └── search.json                  # Instant site-wide search index
├── models/                          # Serialized scikit-learn models & scaler pipelines
├── notebooks/                       # 5 core pipeline chapters (Jupyter notebooks)
│   ├── 01_data_extraction.ipynb
│   ├── 02_feature_engineering.ipynb
│   ├── 03_model_training.ipynb
│   ├── 04_monte_carlo_sim.ipynb
│   └── 05_conclusions_and_reflections.ipynb
├── outputs/                         # Final simulation projections & comparison CSVs
├── powerbi/                         # Business intelligence data model & .pbix file
│   ├── data/                        # 8 normalized star-schema CSV tables
│   └── nba26-27.pbix                # Official Power BI Desktop dashboard file
├── _quarto.yml                      # Quarto publication configuration
├── index.qmd                        # Quarto landing page source
├── requirements.txt                 # Python dependencies
└── README.md                        # Portfolio documentation & project overview
```

---

## Notebook Structure & Pipeline Chapters

The pipeline is organized into five sequential, reproducible chapters mirroring an enterprise sports analytics workflow:

1. **[`01_data_extraction.ipynb`](notebooks/01_data_extraction.ipynb) — Data Extraction & Integrity:** Raw box score ingestion, feature extraction, missing value auditing, and data sanity checks across 2,460 regular season games.
2. **[`02_feature_engineering.ipynb`](notebooks/02_feature_engineering.ipynb) — Feature Engineering & Roster Valuation:** Dean Oliver Four Factors, pace adjustments, Bayesian player shrinkage ($K=8.0$, replacement level $3.90$), and rookie draft-slot priors.
3. **[`03_model_training.ipynb`](notebooks/03_model_training.ipynb) — Machine Learning Win Models:** Rolling window causal features, chronological train/test splitting, ROC-AUC benchmarking, and feature importance analysis.
4. **[`04_monte_carlo_sim.ipynb`](notebooks/04_monte_carlo_sim.ipynb) — Monte Carlo Playoff Simulation:** 10,000 full-season simulations, tiebreakers, Play-In tournament brackets, and Best-of-7 series mechanics.
5. **[`05_conclusions_and_reflections.ipynb`](notebooks/05_conclusions_and_reflections.ipynb) — Conclusions, Reflections & Post-Mortems:** Engineering post-mortems, look-ahead leakage traps, superstar weighting discoveries, and domain retrospectives.

---

## Publishing Format

This project follows a **narrative-first publishing standard**: all notebooks are compiled to responsive, dark-themed static HTML and hosted through GitHub Pages, rather than presented as raw notebook dumps.

- **Interactive Portal:** [https://lianlianliann.github.io/nba-26-27-predictor/](https://lianlianliann.github.io/nba-26-27-predictor/)
- To inspect or run the technical notebooks directly, see the [`notebooks/`](notebooks/) directory above.

---

## Author

**Lian Luigi Paredes**  
[GitHub Profile](https://github.com/lianlianliann)
