# Lighting comfort and zone peak demand: project plan

**Business question:** How much can peak electricity demand be reduced in each zone without compromising lighting comfort?

**Business impact:** give facilities managers a defensible zone-level assessment, protect users’ visual comfort, and identify the measurements needed before investing in lighting controls. Any financial benefit depends on the actual electricity tariff.

## 3. Data Scope

- **Broad EDA:** all 14 local CU-BEMS CSVs, Floors 1–7, July 2018–December 2019, covering 33 floor-zone pairs. There are no 2017 files. The incomplete 2019 Floor 6 tail remains part of the expected coverage denominator.
- **Manageable study:** Floor 2, April 2019, with results also calculated for all other zones. Floor 2 is chosen for manageable scope and sensor availability, not a promised saving.
- **Earlier calibration:** March 6–31, 2019 fixes each zone’s weekday-operating 95th-percentile demand trigger before April is evaluated. The March start is provisional; coverage determines whether calibration is usable.
- **Core signals:** timestamps, floor-zone identifiers, lighting kW, same-zone lux, and total observed circuit kW. AC and plugs establish the total-demand baseline but are not controlled. Temperature/RH remain outside the lighting-reduction analysis.
- **Missing evidence:** actual occupancy, task-plane illuminance, glare/uniformity, complaints, dimming commands, fixture response and utility main-meter/tariff information. No substitute columns or achieved savings are invented.

### Cleaning and assumptions

Both notebooks use complete **15-minute mean kW** intervals. Each observed power circuit must have all 15 valid minute readings; missing circuits are never zero-filled. Lux screening uses the minimum valid reading across the same zone’s sensors and all 15 minutes. Zero readings are retained. Large positive power peaks are investigated rather than automatically removed.

Parse ISO and slash dates explicitly using file-wide day/month evidence. Audit invalid dates, duplicates, expected timestamps, nonnumeric/nonfinite readings, negative power/lux and sensor outages. Reindex to July–December 2018 and January–December 2019 without interpolation. The focused notebook quarantines all duplicate timestamps; the broad EDA removes exact duplicate rows and quarantines conflicting timestamps. This difference does not affect the current files, which have no duplicate rows in the audit.

The source clock is assumed Bangkok local time. Weekday 08:00–18:00 is a schedule proxy, not observed occupancy. The default **500-lux target, 10% margin and 20% dimming cap are illustrative assumptions**, not verified zone-specific requirements. Test 300/500/750 lux and 10/20/30% caps, without lowering requirements merely to produce savings.

### Common April assessment gates

| Gate | Proposed requirement |
|---|---|
| Earlier power calibration | At least 200 valid operating intervals and 80% of expected March operating intervals |
| Evaluation baseline | At least 95% of expected April power intervals |
| Operating comfort evidence | At least 80% of expected April operating intervals with paired power/lux |
| Individual control interval | All 15 power/lux minutes valid, lights on, demand at/above the frozen trigger, lux above target plus margin |

These are analyst screening gates, not industry standards or proof of comfort. Zones failing a gate retain their observed demand baseline but have **unavailable supported reduction estimates (NaN)**. No sensor means comfort cannot be assessed.

## 4. Analytics plan (EDA)

The friend’s notebook keeps its existing eight-section guide. Its broad EDA is followed by the common chronological assessment within the existing scenario section. The focused notebook develops the same April assessment in more detail.

| Step | Analysis | How it answers the question |
|---|---|---|
| 1 | Read and audit every floor/year CSV | Establish trustworthy scope and reveal truncated periods |
| 2 | Map zones, circuits and lux availability; inspect monthly coverage, zeros and flat readings | Identify which zone-periods support comfort screening |
| 3 | Calculate zone peaks, lighting at each peak and weekday/hourly patterns | Quantify lighting’s leverage and when demand is high |
| 4 | Explore lighting-versus-lux relationships and measured below-target observations | Identify possible headroom or measurement/lighting concerns; avoid causal claims |
| 5 | Fix March triggers and evaluate April on the same valid power intervals | Provide an earlier-calibrated, paired zone comparison |
| 6 | Recompute maxima after simulated reductions; align all zones before summing | Detect relocated peaks and distinguish zone from coincident building benefit |
| 7 | Vary task targets/caps, inspect peak quality and test calculation safeguards | Show dependence on assumptions and prevent artificial savings |
| 8 | Export answer tables, figures, audits and parameters | Make the assessment reviewable and reproducible |

The broad notebook also retains **annual retrospective screening**. It applies dimming only at high-demand working intervals. Its same-year thresholds describe historical opportunities and are not a held-out controller test. April uses the earlier March thresholds in both notebooks.

### Conditional scenario and fair comparison

At eligible intervals:

`fraction = min(cap, max(0, 1 − target_lux × (1 + margin) / minimum_lux))`

`scenario_kW = observed_total_kW − lighting_kW × fraction`

`peak_reduction_kW = max(observed_total_kW) − max(scenario_kW)`

Always compare identical valid power rows, including missing-lux periods and nonoperating hours, where no action occurs. Do not restrict the baseline to bright intervals. Recompute the maximum over the whole supported evaluation period because dimming may move the peak.

This assumes proportional electric-light response and nonnegative daylight. Scaling all measured lux is conservative only under those assumptions. Sensor placement and fixture behaviour are unknown. The interval minimum uses hindsight, so this is **offline screening, not real-time control or experimentally verified comfort**. No causal savings confidence interval can be derived from these assumptions alone.

A supported zero means no reduction under the rule. An unavailable estimate means insufficient evidence. Neither establishes zero physical savings potential. Low complete-zone coverage prevents a reliable full-month building-peak claim. The unrestricted lighting-only ceiling in the focused notebook ignores comfort and is not a recommended saving.

## 5. Expected Output

1. **Two executed notebooks:** the existing broad EDA guide and the detailed April study, with matching common-April assessment methods.
2. **Data-quality evidence:** file coverage against full calendars, timestamp audits, circuit/sensor availability and monthly power/lux completeness.
3. **Zone baseline:** observed peak kW, timing, lighting contribution and measured lux context, with coverage qualifications.
4. **Business-answer table for every zone:** observed baseline peak, recomputed scenario peak, supported reduction in kW and %, March trigger/calibration coverage, April coverage, active intervals and assessment status. Proven achievable comfort-preserving savings remain unavailable until tested.
5. **Sensitivity and coincidence:** target/cap sensitivity, original-peak versus recomputed-peak effects, and aligned observed submeter totals with complete-interval coverage.
6. **Action assessment:** identify a measurement-validation site; propose a dimming trial only if task-plane headroom is verified.

Generated results are organised as:

- `outputs/focused_april/` — detailed notebook tables, figures and audits.
- `outputs/full_period_eda/` — friend’s EDA, annual screening and common-April exports.

The two folders prevent accidental overwriting. No separate Python helper scripts are required.

## 6. Action (KPI)

**Current action:** validate sensor placement and occupied task-plane lighting adequacy before reducing lighting. The existing low ambient readings do not establish compliant task-plane headroom, or prove occupied spaces are underlit. Do not recommend curtailment solely because a circuit uses electricity outside assumed work hours.

If measurements confirm headroom at relevant peak times, test a small dimming step in one zone using live task-plane feedback, manual override and immediate restoration on a breach or sensor failure. Confirm fixture dimmability first. Randomise eligible peak events or use randomised daily switchbacks; freeze triggers, targets and exclusions before the trial. Record occupancy, daylight context, feedback and equipment availability.

| KPI | Definition | Proposed decision rule |
|---|---|---|
| Primary demand benefit | Matched control minus treatment 15-minute peak-event kW; also report % | Positive measured effect with day-clustered uncertainty; numeric target set after fixture calibration |
| Monthly zone peak | Credible control-counterfactual maximum minus treatment maximum over the same supported calendar | Report separately from event averages; do not claim a peak effect without a credible counterfactual |
| Lighting guardrail | Occupied task-plane minutes below approved target / valid occupied measured minutes | No increase versus control; restore lighting immediately on a breach |
| Measurement reliability | Valid occupied task-plane lux minutes / expected occupied minutes | Proposed ≥95% coverage; disable dimming on sensor failure |
| User experience | Complaints per occupied person-hour; overrides per treatment event | No increase versus control; review all overrides and glare/uniformity/flicker concerns |
| Secondary energy benefit | Matched control minus treatment lighting kWh | Report separately from peak kW and only if comfort safeguards pass |
| Coincident building benefit | Control-counterfactual versus treatment main-meter maximum | Verify coincidence and complete coverage before claiming building/tariff benefits |

Occupancy, task-plane and feedback KPIs cannot be filled from the historical dataset alone. Set numeric savings targets after calibration; do not equate 20% lighting dimming with 20% total-zone demand reduction. Expand only after measured electrical benefit and comfort safeguards are both supported.

## Sources

- [CU-BEMS original paper](https://doi.org/10.1038/s41597-020-00582-3)
- [Original dataset](https://doi.org/10.6084/m9.figshare.11726517)
- Local inputs: `../data/*Floor*.csv`, unchanged by both notebooks.

The sources establish provenance and measurement context; they do not validate this project’s illustrative task targets or proportional dimming model.
