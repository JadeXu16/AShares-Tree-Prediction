# Tree-Based Sorts: Predicting Future Returns Using Past Returns of A-shares

Undergraduate thesis, Financial Engineering, Wuhan University (2024). Advisor: Prof. Bin Li.
Full text: [thesis.pdf](thesis.pdf)

## Question

Do past monthly returns carry information about future returns in China's A-share market, and if so, how can many
past-return signals with nonlinear interactions be modeled jointly rather than one factor at a time?

## Approach

- **Features.** For each stock and month, the cross-sectional decile rankings of its most recent 25 monthly returns.
- **Model.** Tree-based conditional portfolio sorts (Moritz & Zimmermann, 2016): a random forest learns how past-return
  rankings jointly sort stocks by expected return, allowing nonlinear effects and interactions.
- **Benchmark.** Fama-MacBeth cross-sectional regressions (linear), with a Lasso variant.
- **Out-of-sample test.** Pseudo out-of-sample with a rolling 10-year training window refit annually; predictions from
  2002 onward form an equal-weighted long-short portfolio (top minus bottom predicted decile), risk-adjusted with the
  Fama-French three-factor and Carhart four-factor models.
- **Interpretation.** Variable importance and partial derivatives identify which past returns matter and in which direction.

## Main results

- The long-short strategy earns about 1.9% risk-adjusted return per month with a Sharpe ratio of 1.62, about 0.4% per
  month above the Fama-MacBeth benchmark.
- Medium- and long-term past returns are the most informative, and they relate to future returns through a nonlinear,
  largely reversal-type relationship driven by interactions among them.

## Data

Monthly A-share returns (with reinvested cash dividends), the risk-free rate, and the three- and four-factor series are
from the CSMAR database, covering January 1991 to December 2023. CSMAR data is licensed, so it is not included here;
place the files under `data/` to run the code.

## Code

Run the scripts from the repository root. `result/` holds intermediate and final outputs.

| Script | Purpose | Reads | Writes |
|---|---|---|---|
| `1 Data Preparation.py` | Build the 25 past-return features for each stock-month | `data/initial_data.csv` | `data/data.csv` |
| `4 Market.py` | Market return series used for comparison | `data/TRD_Mnth.csv` | `result/market_ret*.csv` |
| `2 Model.py` | Random forest sorts, long-short strategy, variable importance, partial derivatives | `data/data.csv`, `data/risk_free_rate.csv`, `result/market_ret*.csv` | `result/` |
| `3 Benchmark Model.py` | Fama-MacBeth and Lasso benchmarks | `data/data.csv` | `result/` |
| `5 Partial Derivative Sum.py` | Aggregate and plot partial derivatives | `result/pd_all.csv`, `result/pd_imp.csv`, `result/pd_2.csv` | `result/partial_derivative.csv` |

Run `4 Market.py` before `2 Model.py`, since the model script reads the market series. Script 5 also expects the
intermediate partial-derivative tables `pd_all.csv` and `pd_imp.csv` in `result/`.

```bash
pip install -r requirements.txt
python "1 Data Preparation.py"
python "4 Market.py"
python "2 Model.py"
python "3 Benchmark Model.py"
python "5 Partial Derivative Sum.py"
```

The code is kept as originally written for the thesis in 2024; only file paths have been made relative.
`Figures and Tables/` contains the figures and tables reported in the thesis.
