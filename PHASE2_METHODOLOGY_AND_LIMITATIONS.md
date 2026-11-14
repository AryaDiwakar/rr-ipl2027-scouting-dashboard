# Phase 2 Methodology and Limitations

**Date:** 13 Nov 2026  
**Project:** Rajasthan Royals IPL 2027 Final Recruitment Intelligence (provisional pass)

## 1. Data sources and coverage
- **Primary (local, proprietary):** `/Data Pack` (2022–2026): IPL, T20I, Emerging Teams Asia Cup 2024, Asia Cup Rising Stars 2025. 141 comps, 883 matches, 200,999 deliveries, 959 ball-by-ball players. No SMAT/Ranji domestic T20 ball-by-ball.
- **Phase 1 derived:** `investigation/data/ball_by_ball_enriched.parquet` and pressure/performance CSVs (reproducible from `investigation/scripts/01_build_base_data.py`, `02_pressure_performance.py`).
- **External (Phase 2):** Official IPLT20/RR pages, reputable reporting for process/trades; ESPNcricinfo stats/records for SMAT aggregates (scorecard-level). All external claims intended to carry URL + publication/access dates in source logs (`investigation/phase2/data/availability_source_log.csv`, `domestic_t20_source_log.csv`).
- **Availability:** `player_availability_registry_2027.csv` / `player_availability_final_2027.csv` — **provisional** (no official 2027 retention/release/auction-registration lists found 13 Nov 2026).

## 2. Pressure methodology (refined)
- **Base:** Pre-delivery match state from enriched ball-by-ball (runs required, balls remaining, RRR, wickets, innings, stage/knockout). Phase 1 heuristic preserved for comparison.
- **Refinements:** Phase splits (powerplay 1–6, middle 7–15, death 16–20), chase vs setting, collapse-context proxy (`wkts_before>=3` or high collapse component), 2024–26 recency splits.
- **Threshold sensitivity:** Per-innings quantile flags compared at q50/q60/q70 (`pressure_model_sensitivity.csv`). Default analysis retains Phase 1 `is_pressure` (~q60) for continuity; rankings may be sensitive.
- **Metrics:** Batting SR/avg/boundary%/dot%/dismissal rate; bowling economy/avg/SR/dot%/boundary concession, by overall/pressure/normal/phase/context. Extras handling follows available fields (approximate).

## 3. Bayesian/reliability methodology
- **Shrinkage:** Empirical Bayes adjustment of pressure SR (batting) and pressure economy (bowling) toward role-bucket priors with prior strength `m=120` pressure balls: `adj = (n*raw + m*mu)/(n+m)`.
- **Reliability:** `reliability_score = 100 * n/(n+m)`; effective sample = pressure balls.
- **Confidence labels (transparent):** HIGH >=120, MODERATE 60–119, LOW 20–59, INSUFFICIENT <20 pressure balls.
- **Uncertainty:** Match-cluster bootstrap CIs (n=120 resamples) for players with >=60 pressure balls (`player_confidence_intervals.csv`). Not all metrics/players bootstrapped.
- **Scoring:** Composite pressure scores use normalized adjusted metric + component rates (documented in `04_refine_pressure_model.py`); suitable for relative screening, not absolute truth.

## 4. Domestic T20 investigation
- **Competitions targeted:** SMAT 2024–25, 2025–26 (latest completed). Additional domestic/India A/emerging coverage **incomplete** in this pass.
- **Evidence type:** Scorecard/aggregate statistics from reputable sources (ESPNcricinfo) and limited reported aggregates; **no ball-by-ball domestic pressure reconstruction** and **no fabricated phase/pressure splits**. `NA` where unavailable.
- **Interpretation:** Domestic and IPL/T20I stats not directly comparable without normalization for role, position, opposition, and competition context.

## 5. Role-fit and RR squad model
- **Squad basis:** RR 2026 official retention/trade base (IPLT20/RR, 15 Nov 2025) and RR 2026 squad listing (accessed 13 Nov 2026) as last verified anchor; **2027 retentions/releases unconfirmed**.
- **Gaps (provisional hypotheses):** Indian middle-order #4–6, finisher #6–7, Indian death/powerplay pace execution, seam AR depth, spin support; overseas specialist-only if non-redundant. Overseas regulations (max 8 squad / 4 XI) to be verified against IPL 2027 rules.

## 6. Scoring framework (transparent, provisional)
- **Eligibility gates:** Exclude `CONFIRMED_RR_RETAINED`, `CONFIRMED_RR_CONTRACTED`, `CONFIRMED_RIVAL_RETAINED`, `RETIRED`, `INACTIVE_OR_UNAVAILABLE`. `UNKNOWN`/`POTENTIALLY_AVAILABLE_UNCONFIRMED` treated as **conditional watchlist only** (not confirmed auction targets).
- **Dimensions (0–100):** Pressure, reliability, role-fit, consistency, recent form, domestic evidence, upside, acquisition feasibility. Weighted overall (pressure 0.22, reliability 0.12, role-fit 0.18, consistency 0.08, recent 0.10, domestic 0.10, upside 0.08, feasibility 0.12) — **heuristic and sensitive**; not optimized to force specific names.
- **Availability note:** No official 2027 lists → feasibility/target status cannot be confirmed; outputs labeled provisional/conditional.

## 7. Limitations and unresolved questions
1. **Official IPL 2027 availability unpublished (critical):** retentions, releases, auction registrations, trades, and contracts for 2027 unverified at player level.
2. **Heuristic pressure model:** not a calibrated win-probability model; venue/opposition/matchup/batter-bowler quality not adjusted.
3. **Sample size:** small-sample emerging players (e.g. Vignesh Puthur ~30 pressure balls) carry high uncertainty; shrinkage mitigates but cannot prove ability.
4. **Domestic evidence gaps:** incomplete candidate coverage, no domestic pressure splits, some reported aggregates pending primary corroboration.
5. **Identity/metadata:** ages, full contracts, fitness, NOCs, and stable ID mapping incomplete for external prospects.
6. **No exhaustive robustness/backtest:** full weight/threshold perturbation grids and chronological predictive backtesting not completed in this pass.
7. **Valuation/market:** no auction prices fabricated; demand/feasibility qualitative.

## 8. Decisions that must wait for official announcements
- Any **confirmed** RR target/retention/release determination.
- Final overseas-slot allocation and redundancy checks vs confirmed RR 2027 core.
- Bids/approaches contingent on **CONFIRMED_RELEASED** or **CONFIRMED_AUCTION_REGISTERED** status and verified eligibility/fitness.
