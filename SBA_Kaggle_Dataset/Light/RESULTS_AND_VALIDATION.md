# Findings and validation: both notebooks

## Business-question answer

**A positive comfort-preserving peak reduction is not established by the historical data.** The maximum valid recorded ambient light is **138 lux**. With the illustrative 500-lux target, 10% margin and 20% cap, both notebooks agree: **14 zones** pass the April evidence gates and have **0 kW supported conditional reduction**, while **19 zones** have unavailable estimates.

A zero supported estimate means no headroom under the stated screening rule. Unavailable means missing sensors, insufficient calibration or incomplete evaluation evidence. Neither means physical savings are impossible. Ambient sensors have not been mapped to occupied task-plane illumination; low ambient readings alone do not prove task-plane noncompliance.

All 300/500/750-lux and 10/20/30% cap sensitivity scenarios also lack positive supported reductions. Do not lower a task target just to manufacture savings.

## Verified evidence

- Both notebooks ingest 14 CSVs and cover all 33 floor-zone pairs in the advertised July 2018–December 2019 calendar. There are no 2017 files.
- Twenty-four zones have lux columns; nine have none. Sensorless zones stay in demand baselines but have unavailable comfort-based assessments.
- The local 2019 Floor 7 file uses day/month/year. File-wide parsing recovers the full year with no invalid dates.
- The local 2019 Floor 6 file ends October 22 at 13:27, contains one malformed trailing date row, and lacks **101,432 expected minutes**. Both notebooks now count the missing tail rather than shortening the denominator.
- The common April gates require at least 200 March operating readings and 80% March calibration coverage, 95% April power coverage and 80% April operating paired power/lux coverage. These are project assumptions, not standards.
- With complete 15-minute power required, F4-Z1 has approximately **1.77%** April baseline coverage. Its supported savings estimate is unavailable.
- Only **8/2,880 April intervals (0.28%)** contain valid demand for every zone simultaneously. Supported-subset maxima cannot establish the full-month building peak.
- Floor 2 has **2,865/2,880 complete simultaneous intervals (99.48%)**, with an observed April submeter maximum of **114.82 kW**. No change is simulated under the default rule. This is an observed circuit total, not an audited main meter.

## Floor 2 illustration

| Zone | Observed April peak, kW | Lighting at that peak, kW | Supported conditional reduction, kW |
|---|---:|---:|---:|
| F2-Z1 | 55.47 | 8.41 | 0.00 |
| F2-Z2 | 46.94 | 2.65 | 0.00 |
| F2-Z3 | 2.10 | 1.10 | 0.00 |
| F2-Z4 | 13.10 | 1.85 | 0.00 |

Lighting contribution measures potential leverage, not safe savings. Complete lighting removal ignores comfort and may relocate the peak. The focused notebook reports that unrestricted ceiling separately.

## Improvements and preserved structure

The friend’s notebook retains its original eight sections and their order. Its annual analysis remains retrospective EDA; annual simulated cuts are now restricted to high-demand work intervals and include the lux margin. The March-calibrated April comparison is added within the existing scenario section. Data eligibility is distinguished from evidence of positive headroom. Tables and figures are exported.

The focused notebook uses the same complete-interval requirement and common-April assessment gates. Unsupported reductions are NaN, and proven achievable reductions are unavailable for every zone. Export paths reflect the actual `Light/` folder. The two notebooks write to separate output subfolders.

## Validation: share with caveats

Both notebooks executed top-to-bottom successfully. Saved outputs contain no execution errors. Their common April baseline peaks, scenario peaks, supported estimates, coverage fields and assessment statuses were independently reconciled, allowing for float precision.

Embedded checks cover unique zone/timestamp grain, complete readings, component reconciliation in the focused notebook, peak-only actions, nonnegative bounded cuts, unavailable-estimate handling, missing-lux safeguards and truncated file coverage. Additional constructed cases verified a positive dimming calculation, relocation of an original 10 kW peak to a remaining 9.5 kW peak, and no action/available estimate for a sensorless zone. These checks validate calculations, not real-world comfort.

No causal lighting-response model is identified. The proportional lux response, fixture dimmability and sensor-to-task-plane mapping remain unvalidated. Interval-minimum lux uses hindsight. The outputs are conditional offline screening, not a deployed controller, financial saving estimate or task-plane compliance determination.

## Action

Choose Floor 2 first for measurement validation. Confirm actual occupancy, approved task-plane requirements, sensor placement and fixture response. A controlled dimming trial is justified only if peak-time task-plane headroom is confirmed. Randomise eligible events or use daily switchbacks, retain manual override and live lux feedback, and report demand benefit with uncertainty alongside comfort and measurement guardrails.

See [PROJECT_SCOPE.md](PROJECT_SCOPE.md) for **3. Data Scope, 4. Analytics plan (EDA), 5. Expected Output and 6. Action (KPI)**.

## Reproduce and inspect

Install `requirements.txt`; select Restart Kernel → Run All in either notebook. No external helper script is needed. Inputs remain unchanged in `../data/`.

- Focused results: `outputs/focused_april/zone_peak_scenarios.csv`, `file_audit.csv`, `column_quality.csv`, `coincident_summary.json` and `sensitivity.csv`.
- Broad EDA results: `outputs/full_period_eda/april_zone_scenarios.csv`, `annual_scenarios.csv`, `file_audit.csv`, `coverage.csv`, `april_sensitivity.csv` and generated figures.

Source: [CU-BEMS original paper](https://doi.org/10.1038/s41597-020-00582-3). Numerical findings above come from the local CSVs and executed calculations.
