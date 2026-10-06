# Project plan: peak demand reduction subject to lighting comfort

**Business question:** How much can peak electricity demand be reduced in each zone without compromising lighting comfort?

This document contains the four requested project sections:

- [3. Data Scope](#3-data-scope)
- [4. Analytics plan (EDA)](#4-analytics-plan-eda)
- [5. Expected Output](#5-expected-output)
- [6. Action (KPI)](#6-action-kpi)

**Problem:** Lighting demand may contribute to zone and building peaks, but curtailment can reduce visual comfort. Quantify lighting's leverage at peak times, identify measured illuminance headroom, and determine where a monitored dimming trial is justified. Do not assume all lighting is waste or that low plug demand means a room is empty.

**Who benefits?** Facilities managers receive a zone-specific intervention shortlist and evidence for sensor improvements. Building users retain lighting safeguards and manual override. University operations can reduce coincident electrical demand if field trials establish a viable intervention. Financial benefit depends on the actual tariff, not on dataset kW alone.

## 3. Data Scope

### Coverage and staged scope

- **All-floor EDA:** all 14 local CSVs in `../../data`, Floors 1–7, July 2018–December 2019. The requested 2017 observations are unavailable. The original CU-BEMS source confirms the actual dates; do not relabel years.
- **Manageable detailed scope:** Floor 2 in April 2019, initially Zone 1, with other Floor 2 zones for comparison. This is an EDA starting point, not a predetermined dimming recommendation. Choose a different sensor-equipped zone if the results do not support Floor 2.
- **Earlier calibration:** March 6–31, 2019 for each zone's peak trigger. Require at least 200 valid assumed-operating demand intervals. This is a screening threshold, not a tariff definition or proof that calibration data are representative.
- **Expansion required by the question:** evaluate April scenarios for every zone; calculate coincident observed submeter totals across all zones. An all-floor demand question cannot be answered by multiplying one floor's savings.
- **Fair scenario comparison:** baseline and simulated demand use identical April power intervals. No before/after improvement claim is made from unequal annual datasets. All available years are used to inspect demand and lux availability.

### Fields and grain

| Field | Use in this project | Important qualification |
|---|---|---|
| Individual AC power, kW | Sum installed AC circuits within each zone; explain load composition | No AC intervention simulated; thermal comfort would need a separate analysis |
| Lighting power, kW | Direct controllable load for the hypothetical scenario | Dimmable hardware and kW-to-lux response are unverified |
| Plug power, kW | Describe residual demand | Not an occupancy sensor; no automatic plug shutdown |
| Ambient light, lux | Evaluate observed deficits and screening headroom | Ambient sensor does not establish task-plane or subjective lighting comfort |
| Temperature, °C | Available in inputs; excluded from business-question analysis | Does not identify lighting-only peak reduction |
| Relative humidity, % | Available in inputs; excluded from business-question analysis | No indoor-air-quality claim |
| Timestamp and floor-zone ID | Align signals and calculate coincident peaks | Bangkok local clock assumed; `F2-Z1` and `F3-Z1` are distinct |

Raw grain is one minute per floor file. Analysis grain is one zone per 15-minute interval. Demand is **mean kW**, not the sum of kW across minutes. Observed energy is `sum(valid minute kW)/60`, in kWh. Confirm the utility demand interval separately.

The actual local schema is authoritative. No lux sensors means comfort cannot be assessed for that zone. Missing circuits within a file are never filled with zero; structurally absent circuit types contribute zero only to the **observed circuit total**, which is not a certified utility meter total. Cross-year schema differences are reported.

### Transparent cleaning and assumptions

1. Parse ISO dates explicitly. For slash dates, determine a file-wide convention using dates with a day greater than 12; reject mixed or wholly ambiguous conventions unless explicitly resolved. The local 2019 Floor 7 file is day/month/year.
2. Audit actual start/end, expected minutes, invalid dates, duplicate timestamp rows, off-minute rows, out-of-period rows, and missing timestamps. Quarantine all duplicate timestamps; do not average conflicting readings.
3. Coerce nonnumeric measurements to missing, audit failures, and mask nonfinite/negative power, negative lux, lux above the documented 10,000-lux sensor limit, RH outside 0–100%, and temperature outside the documented 0–90°C hardware range. Retain zero readings.
4. Reindex to the advertised year coverage (July–December 2018; January–December 2019), exposing truncated files and gaps. Do not interpolate, carry forward, or replace missing readings with zero. The local 2019 Floor 6 file ends October 22 and contains a malformed trailing row.
5. Sum zone power only where every observed circuit is valid at the same minute. Average component power over those same minutes. EDA demand intervals need at least 14/15 valid power minutes. Dimming requires all 15 power and lux minutes.
6. Retain large power observations, flag robust high outliers and repeated values, and inspect extreme pilot minutes. Investigate meter faults before interpreting maxima. Coverage tables accompany peak and energy metrics.
7. Assume weekday 08:00–18:00 operation; treat this as a schedule assumption, not occupancy. Test alternate hours. Holidays and academic schedules are unobserved.
8. Use illustrative 300/500/750-lux target sensitivity and 10/20/30% dimming caps. The default 500-lux target plus 10% margin is not an approved zone-specific standard. Never transfer an office-task target automatically to staircases or corridors.

No outdoor dataset is added: it is not needed for the first EDA and cannot establish a causal lighting response. The original source is cited for provenance and known sensor limitations. Pilot occupancy logs, task-plane measurements, fixture tests and user feedback would be new measured data, not invented columns.

## 4. Analytics plan (EDA)

| Analysis | Evidence it provides | Decision supported |
|---|---|---|
| File inventory, timestamp audit, schema drift | Actual years, floors, circuit/sensor matches, truncated files | Define valid scope and comparison periods |
| Missingness by column, zone and month | Lux outages, absent sensors, power completeness | Determine which zones can support screening |
| Zero rates, unique values, flat-value pairs, robust outlier flags | Suspicious signals versus legitimate off/dark periods | Identify measurements needing inspection |
| Zone peaks, P95 demand, lighting share at the actual peak | Whether lighting has enough leverage over zone demand | Avoid recommending lighting control where AC dominates |
| Pilot hourly profiles, weekdays/weekends, AC/light/plug mix | Peak timing and operating patterns | Decide when a pilot should run |
| Operating lux distribution and lighting-versus-lux scatter | Deficit risk and possible headroom | Prioritise lighting adequacy checks or a dimming trial |
| Paired April lighting-only scenario | Conditional kW and kWh changes by zone | Quantify a screening opportunity under stated assumptions |
| Recomputed zone and coincident peaks | Peak movement and time alignment | Distinguish event saving from monthly/building peak reduction |
| Target/cap/schedule sensitivity | Dependence on assumptions | Avoid claiming one arbitrary assumption as certainty |

### Scenario method and interpretation

The March 95th percentile of operating zone demand fixes the trigger before the April evaluation. At April trigger intervals, eligible zones with full valid data and minimum lux above the guard use:

`dim_fraction = min(cap, max(0, 1 − target_lux*(1+margin)/minimum_lux))`

`saved_kW = lighting_kW * dim_fraction`

`scenario_total_kW = observed_total_kW − saved_kW`

`conditional_peak_reduction_kW = max(observed_total_kW) − max(scenario_total_kW)`

Minimum lux is taken over all sensors present in the zone and all 15 minutes. Missing lux, absent sensors, insufficient earlier calibration, or incomplete action intervals produce no simulated action. Every valid baseline-power interval stays in the paired comparison, including weekends and missing-lux periods. Excluding low-lux or missing-lux intervals from the baseline could manufacture savings.

The scenario assumes proportional electric-light lux response to lighting power and nonnegative daylight. Scaling all measured lux by `1−dim_fraction` is then a conservative proxy, but the proportional response, sensor placement and fixture capability are unverified. The offline interval minimum also uses information unavailable at the interval's start. This is **conditional retrospective screening**, not a demonstrated real-time controller or causal savings estimate.

Report an unrestricted lighting-only ceiling separately: baseline peak minus the recomputed peak if all lighting were removed. This is an engineering bound that ignores comfort, not an actionable saving. Never sum independent zone peaks to infer a building peak.

## 5. Expected Output

1. **Executed Jupyter notebook** with all code, assumptions, data quality tables, EDA charts, scenario results, sensitivity and automated calculation checks.
2. **Audit exports:** input inventory, file audit, column quality, schema, zone/sensor map and monthly coverage.
3. **Baseline tables:** zone/year observed peaks and coverage, lighting contribution at peak, observed energy and measured lux deficits. Annual figures describe the input coverage, not measured improvement.
4. **Business-answer table for all zones:** baseline peak, recomputed conditional peak, reduction in kW and %, original-peak savings, unrestricted lighting-only ceiling, calibration size, measured lux coverage, active intervals, and assessment status.
5. **Coincident results:** aligned baseline/scenario submeter demand, with no extrapolation to unmetered equipment or missing intervals.
6. **Action shortlist or evidence that dimming is unsupported.** A zero scenario estimate is an informative result. It must not be rewritten as evidence of zero physical potential.

Read `outputs/zone_peak_scenarios.csv` for exact zone results. The notebook and `RESULTS_AND_VALIDATION.md` describe the executed findings and their interpretation. Generated charts, tables and audits stay in `Light/outputs/`; the notebook recreates them on rerun.

## 6. Action (KPI)

### Action sequence

1. Facilities staff review zone function, fixture dimmability, task-plane requirements, sensor mapping, unexplained spikes and existing lighting deficits. Fix measurement/lighting inadequacy before trying to reduce light.
2. If measured headroom exists at relevant peak times, pilot real-time feedback dimming in one sensor-equipped zone. Start at 10%, retain manual override, and increase only after the approved comfort guardrails pass. Revert on low task-plane lux or sensor failure. Do not automatically switch off lights based on plug load or assumed office hours.
3. Randomise eligible peak events between unchanged lighting and dimming, or use randomised daily switchbacks. Freeze trigger, zone, task target and exclusion rules beforehand. Log occupancy, task-plane lux, daylight context, complaints, overrides and equipment availability.
4. Report event-level demand effects with day-clustered uncertainty, comfort outcomes and coverage. Separately assess monthly peak reduction using a credible matched control counterfactual; an event-average effect is not the maximum-demand effect.
5. Expand only after both electrical benefit and comfort outcomes are supported. Verify coincidence with building/main-meter peaks before claiming a building or tariff benefit.

### KPI framework

| KPI | Formula / unit | Baseline and evidence | Pilot success criterion |
|---|---|---|---|
| Primary: peak-event zone demand | Matched control 15-minute mean kW minus treatment kW; report kW and % | Randomised eligible events in the same zone | Positive measured effect with uncertainty; numeric target set after fixture calibration |
| Monthly zone peak | Control counterfactual maximum kW minus treatment maximum kW over equal supported periods | Prespecified counterfactual with matched schedules and coverage | Positive reduction, reported separately from event average |
| Comfort deficit rate | Occupied measured minutes below approved task-plane lux target / valid occupied measured minutes | Trial control and treatment, actual occupancy | No increase vs control; immediate revert on task-plane breach |
| Measurement coverage | Valid occupied lux minutes / expected occupied minutes | Independent occupancy/task-plane logs | At least 95%; sensor failures disable dimming |
| User comfort | Complaints per occupied person-hour; overrides per treatment event | Trial feedback and logs | No increase relative to control; review every override |
| Secondary lighting energy | Control lighting kWh minus treatment lighting kWh over matched periods | Measured circuit power, supported minutes | Positive saving without comfort degradation |
| Coincident building demand | Maximum aligned main-meter demand under control minus treatment | Main meter and synchronised zone data | Demonstrable coincident reduction, not sum of zone reductions |

The 95% coverage criterion is a proposed project gate, not a measured outcome or external standard. Do not promise 10% total-demand reduction because 10% dimming is offered: lighting may be only a small portion of zone demand. Lux alone cannot measure glare, uniformity or subjective comfort; include those in the site trial.

## References and limitations

- Pipattanasomporn et al., *CU-BEMS, smart building electricity consumption and indoor environmental sensor datasets*, Scientific Data 7, 241 (2020): https://doi.org/10.1038/s41597-020-00582-3
- Original data archive: https://doi.org/10.6084/m9.figshare.11726517
- Local analysis inputs: `../../data/*Floor*.csv`; no source files modified.

The original paper establishes dates, variables, sensor hardware limits and a long maintenance outage. No claim of a mandatory 500-lux requirement is made here. Confirm task-specific requirements with the building owner and applicable current standards before a trial. The two images mentioned in the request were not present in this session; sections follow the requested numbered deliverables directly.
