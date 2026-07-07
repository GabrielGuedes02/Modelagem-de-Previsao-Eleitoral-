# Electoral Forecast Modeling in Brazil — Literature Review

**Author:** Gabriel Guedes Mascarenhas Lopes
**Date:** 2026-02-09

## Overview

This project implements and compares three classic econometric models of retrospective/economic voting — **Abramovitz (2008)**, **Lewis-Beck (2008)**, and **R. Lins (2016)** — applied to Brazilian presidential elections, using state-level panel data. The models are estimated on historical elections (2018 and 2022) and used to generate a conditional out-of-sample forecast for the **2026** presidential election.

The output is an R Markdown (`.Rmd`) report that renders to HTML, with a custom theme, methodology write-up, model equations in LaTeX, regression diagnostics, and a summary table comparing observed vs. predicted incumbent vote share across models and years.

## Data Sources

- **TSE (Tribunal Superior Eleitoral)** — valid vote percentages by state (UF), retrieved via the `electionBR` package.
- **IBGE / Ministry of Social Development** — Bolsa Família Index (IBF), share of households receiving the Bolsa Família cash-transfer program.
- **Datafolha** — incumbent president's approval rating.
- **2022 Census** — state population, used for population-weighted national aggregation.

All data is expected in a single file, `BaseDados1.csv`, located in the project root (loaded via `here::here()`).

## Models

### 1. Abramovitz (2008) — Panel Random Effects (GLS)
$$V_{it} = \alpha + \beta_1 \text{Var}_{it} + \beta_2 \text{IncAprov}_t + \gamma D_t + u_i + \varepsilon_{it}$$
Includes state economic growth, presidential approval, and an institutional dummy for a party's third consecutive term (2002, 2010).

### 2. Lewis-Beck (2008) — Panel Random Effects (GLS)
$$V_{it} = \alpha + \beta_1 \text{Var}_{it} + \beta_2 \text{IncAprov}_t + u_i + \varepsilon_{it}$$
A more parsimonious specification with only economic growth and approval.

### 3. R. Lins (2016) — Cross-Sectional OLS
$$V_i = \alpha + \delta \, \text{IBF}_i + \varepsilon_i$$
Relates incumbent vote share to the intensity of the Bolsa Família cash-transfer program, estimated separately for each election year.

All panel models are estimated with population-weighted GLS and cluster-robust standard errors (CR2, clustered by state).

## 2026 Forecast Assumptions

- Projected GDP growth: **2.6%**
- Incumbent approval ("Ótimo/Bom"): **~30–33%**
- State-level growth for 2026 is imputed proportionally to each state's historical growth pattern relative to the national average.

## Repository Structure

```
.
├── Modelagem_Previsao_Eleitoral.Rmd   # Main R Markdown report
├── BaseDados1.csv                     # Input dataset (not included)
├── custom_style.css                   # Custom CSS for HTML output
├── Tabela - Renno2022.png             # Reference table image (predictive accuracy, Rennó 2022)
├── Tabela - R.Lins.png                # Reference table image (predictive accuracy, R. Lins)
└── README.md
```

> **Note:** Image paths in the current `.Rmd` are hard-coded to a local machine (`C:/Users/guede/OneDrive/...`). Update these paths to a relative path (e.g. `images/`) before rendering on another machine.

## Requirements

- R (≥ 4.0)
- R packages:
  - `tidyverse`
  - `janitor`
  - `here`
  - `rio`
  - `fixest`
  - `plm`
  - `clubSandwich`
  - `wooldridge`
  - `knitr`
  - `kableExtra`

Install dependencies:

```r
install.packages(c(
  "tidyverse", "janitor", "here", "rio", "fixest",
  "plm", "clubSandwich", "wooldridge", "knitr", "kableExtra"
))
```

## How to Run

1. Place `BaseDados1.csv` in the project root.
2. Fix the hard-coded image paths (see note above) or remove those chunks if the images are unavailable.
3. Open the `.Rmd` file in RStudio and click **Knit**, or run from the console:

```r
rmarkdown::render("Modelagem_Previsao_Eleitoral.Rmd")
```

4. The rendered HTML report will include the comparison table and full regression output for all three models, plus the 2026 forecast.

## Theoretical Framework

Forecasts follow the **conditional prediction** framework described by Wooldridge (2016, *Introdução à Econometria*): the expected value of the dependent variable is predicted conditional on given regressor values, using coefficients estimated from the historical panel:

$$\widehat{E}(y_i \mid X_i) = \hat{\beta}_0 + \hat{\beta}_1 X_{1i} + \hat{\beta}_2 X_{2i} + \cdots$$

National forecasts aggregate state-level predictions using population weights from the 2022 Census:

$$\widehat{V}_t^{\text{National}} = \frac{\sum_{i=1}^{N} \widehat{V}_{it} \cdot \text{Pop}_i}{\sum_{i=1}^{N} \text{Pop}_i}$$

## Key Findings (Historical Fit)

- **Presidential approval** is the strongest and most consistent predictor of incumbent vote share across both panel models.
- **State-level economic growth** shows no statistically significant effect in either panel specification.
- The **third-term dummy** (Abramovitz model) is negative and significant, suggesting an electoral penalty for prolonged institutional continuity.
- The **Bolsa Família Index** is negatively associated with incumbent vote share in the 2022 cross-section, with a relatively high R² (0.48) for a univariate model.

## License

Not specified — add a license file if you intend to share or reuse this code.
