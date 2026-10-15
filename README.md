# Value-at-Risk of a Ruble-Based Portfolio Through the 2022 Shock

A Russian investor holds German and US stocks (40% DAX-like with beta 1.3, 60% NASDAQ-100-like with beta 1.6), measured in rubles. How did the portfolio's risk change between 23 February 2022, the day before the invasion of Ukraine, and 9 March 2022, two weeks into the ruble collapse?

The full analysis, with outputs, is in [`ruble_portfolio_var.ipynb`](ruble_portfolio_var.ipynb).

## Results (99% VaR, fraction of portfolio value)

| Method | 23 Feb 2022 | 9 Mar 2022 | Change |
|---|---|---|---|
| Normal parametric, systematic, 1 day | 5.2% | 17.4% | ×3.3 |
| Normal parametric, systematic, 10 days | 16.4% | 54.9% | ×3.3 |
| Equity stand-alone, 10 days | 16.2% | 18.6% | ×1.2 |
| Forex stand-alone, 10 days | 12.3% | 56.3% | ×4.6 |
| Historical, equal weights, 1 day | 6.1% | 6.3% | ×1.04 |
| Historical, age weights (BRW), 1 day | 5.5% | 8.5% | ×1.5 |
| Historical, volatility weights (Hull-White), 1 day | 7.2% | 23.9% | ×3.3 |

The jump comes from the currency: annual EWMA forex variance rose about twentyfold. Equity and forex are negatively correlated in ruble terms, so diversification holds. Equal-weight historical VaR barely reacts to the regime change; volatility-weighted VaR matches the parametric estimate.

## Method

- **Returns:** log returns of the DAX, NASDAQ-100, EUR/RUB and USD/RUB. Equity factor = Σ wᵢβᵢyᵢ, forex factor = Σ wᵢxᵢ, portfolio = equity + forex.
- **EWMA (RiskMetrics, λ = 0.94):** volatilities, covariance and correlation, annualised over 250 days.
- **Parametric VaR:** Φ⁻¹(0.99) √(h/250 · θᵀΣθ), systematic with θ = (1, 1), stand-alone with (1, 0) and (0, 1).
- **Historical VaR:** equal-weighted 1% quantile; age-weighted (Boudoukh, Richardson and Whitelaw) with weights λᵏ and a weighted quantile; volatility-weighted (Hull and White) with returns rescaled by σ̂_T / σ̂_t.
- **Checks** in the notebook: weighted-quantile unit cases, square-root-of-time scaling, sub-additivity of the systematic VaR.

## Run it

```bash
pip install -r requirements.txt
jupyter nbconvert --to notebook --execute ruble_portfolio_var.ipynb --output executed.ipynb
```

`data/market_data.csv` is a snapshot of Yahoo Finance daily closes (`^GDAXI`, `^NDX`, `EURRUB=X`, `RUB=X`, March 2019 to March 2022); delete it to download afresh. The GitHub Actions workflow executes the notebook on every push.
