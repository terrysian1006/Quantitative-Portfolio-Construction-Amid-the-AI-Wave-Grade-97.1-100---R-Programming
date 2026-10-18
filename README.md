# Human Premium vs. the AI Shock: Building a Defensive Portfolio Through the Generative-AI Boom

*Out-of-sample backtest of seven portfolio strategies across 37 US-listed assets, Dec 2021 – Dec 2024 · R (quadprog, PerformanceAnalytics)*

📄 Full report: [English](Financial_Data_Analysis_Final_Project_EN_TerryLai.pdf) · [中文](Financial_Data_Analysis_Final_Project_TerryLai.pdf) · 💻 Code: [`Financial_Data_Analysis_Final_Project_TerryLai.R`](Financial_Data_Analysis_Final_Project_TerryLai.R)

---

## The answer up front

**A long-only minimum-variance portfolio delivered the best risk-adjusted outcome of all seven strategies through the generative-AI shock: a Sharpe ratio of 1.25, versus 0.84 for an equal-weight portfolio, with the shallowest drawdown (6.5%) and low turnover.** Three findings support this:

1. **AI split the market into winners and losers, which makes naive diversification expensive.** The "AI-disrupted" group carried roughly 2.4× the volatility of the benchmark group, and the best single asset peaked at more than 6× its starting value while the worst kept only 10–20% of it.
2. **Unconstrained optimization did not survive contact with that market.** Allowing short sales turned estimation error into extreme positions: the tangency portfolio needed daily turnover of 34× its capital and produced numerically meaningless results.
3. **One simple constraint (no short sales) turned the optimizer into the most reliable strategy.** It cut volatility nearly in half and lowered tail loss by about 45% relative to 1/N, while giving up only about two percentage points of annual return.

---

## Situation, complication, question

- **Situation.** Since late 2022, generative AI has concentrated capital in a narrow set of large-cap tech and AI-infrastructure names, lifting broad indices such as the S&P 500 and Nasdaq-100.
- **Complication.** Beneath the index, pricing has diverged sharply. Business models built on routine knowledge work face re-rating, while businesses protected by IP, physical presence or security needs may command a "human premium". Daily returns in this period are non-normal and fat-tailed, which is exactly where mean-variance optimizers are known to fail.
- **Key question.** *Can a covariance-driven portfolio, with no return forecasts, automatically de-risk away from AI-exposed assets, and which real-world constraints make it robust enough to use?*

## Approach

| Step | What was done |
|---|---|
| **Universe** | 37 assets in five thesis-driven groups: AI-disrupted (e.g., CHGG, FVRR, UPWK), IP moats (DIS, NYT, SONY), deepfake and cyber defenders (CRWD, PANW, FICO), human-experience economy (LYV, LVMUY, MAR, SBUX), and benchmarks / safe havens (SPY, QQQ, GLD, TLT, XLU) |
| **Data** | Yahoo Finance daily adjusted close, 774 trading days (1 Dec 2021 – 31 Dec 2024) |
| **Strategies** | Passive: 1/N, buy-and-hold. Optimized with quadratic programming: GMVP, no-short GMVP, target-return MVP (10% p.a.), no-short MVP, and tangency |
| **Backtest** | Rolling 252-day estimation window, re-optimized daily, 522 out-of-sample days (Dec 2022 – Dec 2024), no look-ahead |
| **Frictions** | Transaction costs charged on actual turnover, including weight drift; risk-free rate 2.5% |
| **Scorecard** | Return, volatility, Sharpe, VaR and expected shortfall (5%), downside deviation, maximum drawdown, turnover, concentration (HHI), short-to-long ratio |

## Key findings

### 1. The AI shock created a K-shaped market where diversification alone carries hidden tail risk

- The AI-disrupted group averaged **49% annualized volatility, about 2.4× the benchmark group (20%)**. Chegg (CHGG) and Fiverr (FVRR) ran at **73.9% and 70.6%**, roughly **4× the S&P 500 (17.5%)**.
- CHGG lost **48.4% in a single day**, a left-tail event that a normal-distribution model treats as essentially impossible.
- Over three years the S&P 500 returned about **35%**. The best asset in the universe peaked at a cumulative return of more than **570%**, while the worst lost **80–90%** of its value.
- **So what:** equal weighting forces an investor to hold the disrupted names at full size. Return dispersion this wide makes risk-aware allocation a necessity rather than a refinement.

### 2. Diversification potential was real, but it sat in a few places

- Gold and long Treasuries were the true diversifiers: their **average correlation with the 28 single stocks was 0.08 (GLD) and 0.07 (TLT)**, versus **0.50 for QQQ**.
- AI-disrupted names were **less** tied to the Nasdaq-100 than expected (average correlation 0.42; CHGG–QQQ only 0.27). The market priced them as a separate risk, not as "tech".
- Luxury and live entertainment moved largely independently of the AI-disrupted group (e.g., LVMUY–CHGG 0.19).

### 3. Out of sample, constraints mattered more than sophistication

| Strategy | Ann. return | Ann. volatility | Sharpe | Expected shortfall (5%, daily) | Max drawdown | Daily turnover |
|---|---:|---:|---:|---:|---:|---:|
| **No-short GMVP** | 12.6% | **8.1%** | **1.25** | **−1.1%** | **6.5%** | 0.03 |
| Buy-and-hold | **16.8%** | 14.5% | 0.99 | −2.0% | 10.4% | 0 |
| GMVP (shorts allowed) | 9.5% | 8.0% | 0.87 | −1.0% | 7.9% | 0.09 |
| 1/N equal weight | 14.8% | 14.6% | 0.84 | −2.0% | 10.0% | 0.01 |
| No-short MVP (10% target) | 8.2% | 8.3% | 0.69 | −1.1% | 8.9% | 0.07 |
| MVP (10% target, shorts allowed) | 6.3% | 8.2% | 0.46 | −1.1% | 11.4% | 0.12 |
| Tangency (shorts allowed) | *unstable* | *unstable* | n/m | n/m | n/m | 34.2 |

- **No-short GMVP led on every risk measure that matters to a real investor.** Against 1/N, it cut volatility by 45%, expected shortfall by 45% and maximum drawdown by a third, and it raised the Sharpe ratio by about 50%. Buy-and-hold earned more in absolute terms by riding the 2023–24 rally, but at 1.8× the volatility.
- **Unconstrained optimizers maximized estimation error.** The tangency portfolio's weights became so extreme that single-day "returns" fell below −100%, which is impossible for a real, funded portfolio. The unconstrained GMVP held short positions equal to **46% of its long book**, which would expose it to margin calls in practice.
- **The no-short constraint also concentrated capital deliberately.** Concentration (HHI) rose from 0.027 for 1/N (fully spread) to 0.21: the optimizer stopped holding everything and focused on low-volatility, low-correlation assets.

## Implications

1. **For investors:** in a regime shift, a long-only minimum-variance rule is a credible defensive core. It needs no return forecasts, trades little, and still captured most of the upside.
2. **For model builders:** treat the no-short constraint as a structural fix for estimation error, not only as a compliance rule. Validate optimizers out of sample with realistic costs before trusting in-sample Sharpe ratios.
3. **For the "human premium" thesis:** the risk data are consistent with it, since physical-moat and safe-haven assets offered lower volatility and lower correlation with disrupted names. Proving that the optimizer actively rotated into those assets requires a weight-attribution analysis, which is the natural next step.

## Limitations and next steps

- **Covariance estimation:** a 252-day sample covariance reacts slowly to volatility clustering. EWMA or GARCH estimates, and Ledoit-Wolf shrinkage for the 37-asset matrix, should stabilize weights further.
- **Single regime, single period:** 522 out-of-sample days cover one bull market. Results should be stress-tested across other regimes and with block-bootstrap confidence intervals on the Sharpe ratios.
- **Survivorship and selection:** the universe was chosen with hindsight about the AI narrative, which can flatter thematic conclusions.

## Reproducing the analysis

```r
# Requires: quantmod, xts, PerformanceAnalytics, quadprog
# plus the course helper file function_FDA.R (not included), which provides
# gmvp_wx_quad, nsgmvp_wx_quad, mvp_wx_quad, nsmvp_wx_quad, tan_wx,
# my_skewness, my_kurtosis and my_acf1.
source("Financial_Data_Analysis_Final_Project_112652022.R")
```

The script downloads prices from Yahoo Finance, builds the return matrix, produces the descriptive statistics and correlation tables, runs the rolling backtest and writes `OoS_Performance_Table_Ultimate.csv`. Update the `setwd()` and `source()` paths before running.

## Skills demonstrated

**Analytics:** time-series data engineering in R, quadratic-programming portfolio optimization, rolling out-of-sample backtesting with transaction costs, and tail-risk metrics (VaR, ES, LPSD, MDD).
**Problem solving:** a hypothesis-driven universe design, evaluation on implementability (turnover, leverage, concentration) rather than return alone, and explicit treatment of model failure.

---

*Final project for Financial Data Analysis (財務金融資訊分析). Final grade: 97.1 / 100. For educational purposes only; not investment advice.*
