# CVA MC Engine — ORE Python

End-to-end **Credit Valuation Adjustment (CVA)** workflow built on the
[Open Source Risk Engine (ORE)](https://github.com/OpenSourceRisk/Engine) Python bindings.

## What the notebook does

| Part | Description |
|:---|:---|
| **1 — Monte Carlo Exposure** | Runs a cross-sectional MC simulation (1-factor LGM model) on a 3-swap portfolio over a biweekly grid. Extracts per-trade EPE/ENE and netting-set profiles. |
| **2 — Collateral (CSA)** | Re-applies the XVA aggregation with an active CSA (threshold EUR 1MM, MTA EUR 100k, MPoR 2W). Reuses the existing NPV cube — no re-simulation. |
| **3 — CDS Bootstrap** | Bootstraps a piecewise-flat hazard rate curve for the counterparty from 7 CDS par spreads (6M → 10Y, 50–130 bps) using `SpreadCdsHelper` + `PiecewiseFlatHazardRate`. |
| **4 — CVA** | Computes CVA via discrete integration: `CVA = LGD × Σ DF(t) × EPE(t) × ΔPD(t)`. Compares uncollateralised vs CSA-reduced exposure. |

## Portfolio

| Trade | Type | Currency | Notional | Maturity |
|:---|:---|:---:|---:|:---|
| Swap 1 | Pay-fixed IRS | GBP | 40MM | 2027 |
| Swap 2 | Receive-fixed IRS | EUR | 30MM | 2026 |
| Swap 3 | Receive-fixed IRS | USD | 50MM | 2024 |

All trades are in netting set `CPTY_A`. Counterparty recovery = 40%.  
As-of date: **2016-02-05**

## Results

| Scenario | Peak EPE | EEPE 1Y | CVA |
|:---|---:|---:|---:|
| No Collateral | EUR 3,572,203 | EUR 2,387,515 yr | EUR 302,206 |
| CSA Threshold EUR 1MM | — | EUR 764,635 yr | EUR 65,229 |
| **CVA reduction** | | **↓68%** EEPE | **↓78.4%** CVA |

## Output charts

| File | Content |
|:---|:---|
| `cva_exposure_nocol.png` | Per-trade EPE and netting-set EPE/ENE |
| `cva_collateral_effect.png` | EPE before/after CSA + 1Y EEPE bar chart |
| `cva_cds_bootstrap.png` | Hazard rate step-function + survival probability curve |
| `cva_summary.png` | EPE overlay · cumulative CVA build-up · CVA bar chart |

## Dependencies

- [ORE Python bindings](https://github.com/OpenSourceRisk/Engine) (`import ORE`)
- `numpy`, `matplotlib`, `pandas`

## Related repo

[CDS Curve Bootstrapping: QuantLib vs ORE](https://github.com/jontymayhew/CDS-Curve-Bootstrapping-QuantLib-vs-ORE)
