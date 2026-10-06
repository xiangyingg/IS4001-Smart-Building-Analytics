# Executed findings and validation

## Answer to the business question

**A positive comfort-preserving peak reduction is not established by these observational data.** The maximum valid ambient-light reading across the local files is **138 lux**. Consequently, the illustrative 500-lux target with a 10% margin permits no dimming in the April 2019 screening scenario: **0 kW simulated peak reduction in all 33 zones**. This is a result of the stated screening rule, not proof that physical savings are impossible. It also does not establish that occupied task planes are underlit; sensor placement and task-plane mapping are unknown.

The same no-action result occurs in the 300/500/750-lux and 10/20/30% cap sensitivity scenarios. Do not lower the target simply to generate savings. Confirm a defensible zone-specific task target and sensor calibration first.

## Verified evidence

- Fourteen local floor/year files were analysed, covering Floors 1–7 in 2018 and 2019. The normal coverage is July 2018–December 2019; there are no 2017 files.
- There are 33 observed zones and 24 zones with lux columns. Nine zones lack lux sensors and cannot support comfort assessment.
- The local 2019 Floor 7 file uses **day/month/year**. File-wide parsing based on unambiguous days recovers January 1–December 31 and monotonic timestamps without invalid dates.
- The local 2019 Floor 6 file ends **October 22, 2019 at 13:27**, has one malformed trailing date row, and lacks **101,432 expected minutes**. No missing minutes are interpolated or treated as zero.
- Several April zones have weak power coverage. In particular, F4-Z1 has about **2.0%** valid 15-minute demand coverage, so its observed maximum is not a reliable full-month peak. Consult each zone's coverage before using its result.
- Only **22 of 2,880 April intervals** have valid demand for every zone simultaneously (**0.76%** coverage). A full-building monthly peak or savings claim cannot be supported by that complete-zone subset. No extrapolation is made.
- Floor 2 has **2,875/2,880** complete simultaneous power intervals. Its maximum supported observed submeter demand is **114.82 kW** in April, with no change under the default no-action scenario. This is an observed circuit total, not a certified main-meter value.

## Floor 2 illustration: lighting leverage at peak

| Zone | Observed April zone peak, kW | Lighting kW at that zone peak | Default conditional reduction, kW |
|---|---:|---:|---:|
| F2-Z1 | 55.47 | 8.41 | 0.00 |
| F2-Z2 | 46.94 | 2.65 | 0.00 |
| F2-Z3 | 2.10 | 1.10 | 0.00 |
| F2-Z4 | 13.10 | 1.85 | 0.00 |

Lighting contribution provides an engineering leverage measure. Removing this lighting entirely would not be a comfort-preserving action, and it may not reduce the recomputed maximum by that full amount. The unrestricted lighting-only peak ceiling is separately exported and explicitly ignores comfort.

## Action linked to the question

Begin with sensor/task-plane validation on Floor 2 and review actual lighting adequacy during occupied peak events. If task-plane measurements identify genuine headroom, perform a small randomised dimming trial with manual override. Measure paired peak-event kW reduction, occupied task-plane lux deficit rate, feedback and coverage. Investigate power-data gaps before making a building-wide monthly peak claim. The dataset alone cannot populate actual occupancy or complaint KPIs.

## Validation assessment: share with caveats

The notebook executes top-to-bottom and exports its calculations, figures and audits. Its analytical checks verify unique zone/timestamp grain, component-to-total reconciliation on common supported minutes, nonnegative bounded savings, recomputed peaks, full-minute action eligibility, and no savings assigned where lux sensors are absent or missing.

The raw CSVs are unchanged. The results are suitable for an EDA project and a measurement-first recommendation. They are not suitable for a guaranteed savings claim, task-plane compliance claim, tariff estimate, or deployed controller specification. The 500-lux target is illustrative; the proportional lux response and dimmable fixture assumptions are unvalidated. The interval-minimum scenario uses hindsight and does not simulate real-time feedback.

Reproduce by installing `requirements.txt`, opening the notebook, and selecting Restart Kernel → Run All. Exact values are in `outputs/zone_peak_scenarios.csv`, `outputs/file_audit.csv`, `outputs/column_quality.csv`, and `outputs/coincident_summary.json`. Parameters and software versions are recorded in the notebook and `outputs/analysis_parameters.json`.

Source provenance: [CU-BEMS original paper](https://doi.org/10.1038/s41597-020-00582-3). Numerical findings above come from the local CSVs, not from assumed building performance or invented interventions.
