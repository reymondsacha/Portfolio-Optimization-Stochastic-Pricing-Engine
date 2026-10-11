# Portfolio Optimization & Stochastic Pricing Engine

A modular Python framework combining modern portfolio theory, statistical factor modeling, and continuous-time stochastic pricing tools. Designed with numerical stability, strict array vectorization, and clean architectural separation of concerns.

---


## Documentation

- 📄 [Read the mathematical derivation](docs\math_derivation\MV_Optimization_Spec.pdf)

## Architecture Overview

The repository is organized into distinct submodules for data ingestion, portfolio analytics, historical backtesting, and stochastic calculus:

```text
├── data/                       # Local cached datasets (Parquet / Hive partitioned)
├── docs/                       # Research documentation and mathematical derivations
├── logs/                       # Application and execution logs
├── notebooks/                  # Interactive research and validation
│   ├── physics/                # SDE simulation, Greeks surfaces
│   └── research/               # Asset allocation tournaments, SVD factor audits
├── results/                    # Exported metrics, backtest curves, plots
├── src/
│   ├── analytics/              # Optimization engines (MVO, Black-Litterman, HRP, PCA)
│   ├── backtest/               # Strategy replay, performance attribution, rebalancing
│   ├── data/                   # DataManager (atomic writes, data cleaning, validation)
│   ├── physics/                # Stochastic models (GBM, Martingale audits, Greeks)
│   ├── utils.py                # Math helpers, matrix diagnostics, logging utilities
│   └── __init__.py
├── tests/                      # Unit and integration test suite
├── backtest_results.csv        # Historical performance run output
├── pyproject.toml              # Dependency and packaging configuration (Poetry)
├── run_analytics.py            # CLI entrypoint for portfolio optimization & factor runs
├── run_backtest.py             # CLI entrypoint for backtest execution
└── run_data.py                 # CLI entrypoint for market data ingestion
```

---

## Core Modules

### 1. Portfolio Allocation & Optimization (`src/analytics/`)
* **Mean-Variance Optimization (MVO):** Markowitz formulation solved via quadratic programming (`scipy.optimize`), supporting long-only constraints, weight bounds, and Ledoit-Wolf shrinkage covariance.
* **Hierarchical Risk Parity (HRP):** Tree-clustering and recursive bisection asset allocation based on correlation distance metrics, avoiding covariance inversion.
* **Black-Litterman Model:** Bayesian equilibrium framework integrating analyst views with market equilibrium priors; calibrated view uncertainty matrices ($\Omega$).
* **Latent Factor Engine:** Truncated SVD / Principal Component Analysis for dimensionality reduction and statistical risk decomposition, separating systematic factor risk from idiosyncratic variance.
* **Macro View Injection:** Projection of portfolio weights onto PCA factor subspaces to enable factor-neutral hedging and regime tilts.

### 2. Stochastic Calculus & Derivative Pricing (`src/physics/`)
* **Stochastic Simulation Engine:** Fully vectorized Geometric Brownian Motion (GBM) path simulator under both physical ($\mathbb{P}$) and risk-neutral ($\mathbb{Q}$) measures.
* **Analytical Oracle:** Vectorized Black-Scholes benchmark with complete analytical Greek evaluations:
  * First-order: Delta ($\Delta$), Vega ($\nu$), Theta ($\Theta$)
  * Second-order: Gamma ($\Gamma$)
  * Cross-derivatives / Shadow Greeks: Vanna ($\frac{\partial \Delta}{\partial \sigma}$), Charm ($\frac{\partial \Delta}{\partial t}$)


### 3. Data Infrastructure & Backtesting (`src/data/`, `src/backtest/`)
* **DataManager:** Download, atomic disk cache, corporate-action sanity checks, and clean return calculation with zero lookahead bias.
* **Backtest Engine:** Event-driven / periodic rebalancing engine for tracking cumulative returns, drawdown dynamics, and Sharpe/Sortino ratios across strategies.

---

## Getting Started

### Prerequisites
* Python 3.10+
* [Poetry](https://python-poetry.org/)

### Installation

```bash
# Clone the repository
git clone <repo-url>
cd QuantLab

# Install dependencies using Poetry
poetry install
```

### CLI Workflows

```bash
# Ingest and update price series
poetry run python run_data.py

# Run portfolio optimization, factor extraction, and BL view injection
poetry run python run_analytics.py

# Execute historical backtests across allocation models
poetry run python run_backtest.py
```

### Running Tests

```bash
# Run test suite
poetry run pytest tests/
```

---

