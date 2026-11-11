# Motor Insurance Risk Pricing Using Poisson and Gamma GLMs

Frequency–severity pricing analysis on French motor third-party liability data.

## Overview
- Validates and explores 677,991 policies and 26,444 claims
- Poisson GLM (log link, log-exposure offset) for claim frequency, with a quasi-Poisson adjustment for overdispersion
- Gamma GLM (log link) for claim severity
- Pure premium = predicted frequency × predicted severity, compared with a constant-severity baseline and a direct Tweedie GLM
- Policy-level train/test split (80/20, seed 42) so claims from one policy never appear in both sets

## Data
Source: CASdatasets, `freMTPL2freq` and `freMTPL2sev`
(https://dutangc.github.io/CASdatasets/reference/freMTPL.html).
The data are not included in this repository. Export them from the CASdatasets R package
to CSV and save them as:

```
data/raw/freMTPL2freq.csv
data/raw/freMTPL2sev.csv
```

## How to run
```
pip install -r requirements.txt
jupyter notebook motor_insurance_pricing.ipynb
```

## Key results (held-out test set, 135,599 policies)
| Model | Predicted cost per policy-year | Portfolio error |
|---|---:|---:|
| Observed | €151.26 | – |
| Poisson + Gamma | €163.53 | +8.12% |
| Poisson + constant severity | €171.37 | +13.30% |
| Direct Tweedie | €167.45 | +10.70% |

Poisson + Gamma came closest to the observed portfolio cost, while Poisson + constant severity had the lowest policy-level Tweedie deviance. The Poisson model overpredicted total test claims by 1.59%. Observed frequency rose from 3.52 to 18.57 claims per 100 policy-years across the predicted frequency deciles.

- Predicted claim count was within +1.59% of observed.
- Observed frequency rose from 3.5 to 18.6 claims per 100 policy-years across predicted risk deciles.
- The Gamma severity model did not beat a constant-severity baseline on claim-level Gamma deviance.

## Limitations
- Single random train/test split; the Tweedie comparison was added after viewing test results.
- Large claims (above the 99th percentile) account for 38% of total cost and affect severity estimates.
- French historical data; not validated for Dutch pricing.
- Pure premium excludes expenses, reinsurance and profit margin.
- No demand or price-elasticity modelling.

## Possible improvements
Cross-validation, sensitivity analysis for large losses, tariff relativities, gradient boosting benchmark, out-of-time validation.
