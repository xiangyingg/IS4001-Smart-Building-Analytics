# Peak Demand Reduction Without Compromising Lighting Comfort

**Business question:** How much can peak electricity demand be reduced in each zone without compromising lighting comfort?

## Business impact

Help facilities managers identify opportunities to reduce peak electricity demand while protecting users’ visual comfort. Financial benefits depend on whether the building’s tariff charges for peak demand.

## Data and analysis

Analyse CU-BEMS data for **Floors 1–7, July 2018–December 2019**, with a detailed Floor 2 study in April 2019. No 2017 data is available locally.

Both notebooks check data quality, identify zone peaks and lighting contributions, assess measured lux, and evaluate conditional dimming scenarios. The broad EDA retains its eight-section guide; the focused notebook develops the March-calibrated April comparison. Both require complete 15-minute readings and recompute peaks on identical supported power intervals.

## Key finding

The maximum valid recorded ambient-light value is **138 lux**. With an **illustrative 500-lux target**, **14 zones** pass the April evidence gates and show **0 kW supported conditional reduction**; **19 zones** have unavailable estimates. No positive reduction is supported under the stated rule.

This does not prove that savings are impossible. Sensor readings have not been mapped to occupied task-plane illuminance, so a positive comfort-preserving reduction cannot yet be established.

## Action and KPIs

Validate sensor placement, zone-specific lighting requirements and fixture dimmability. If peak-time lighting headroom is confirmed, run a controlled dimming trial with real-time lux feedback and manual override.

- **Demand:** matched peak-event reduction in 15-minute demand, measured in kW and %.
- **Comfort:** no increase in occupied minutes below the approved task-plane lux target or in comfort complaints.
- **Reliability:** proposed minimum 95% valid occupied lux coverage; disable dimming on sensor failure.
- **Building impact:** confirm savings coincide with building peaks before claiming building-wide demand reduction.

## Deliverables

| File | Purpose |
|---|---|
| [PROJECT_SCOPE.md](PROJECT_SCOPE.md) | 3. Data Scope; 4. Analytics plan (EDA); 5. Expected Output; 6. Action (KPI) |
| [SBA_Peak_Demand_Lighting_EDA.ipynb](SBA_Peak_Demand_Lighting_EDA.ipynb) | Full code, EDA and zone-level results |
| [electricity_lighting_eda.ipynb](electricity_lighting_eda.ipynb) | Eight-section broad EDA guide, annual screening and the same April assessment |
| [RESULTS_AND_VALIDATION.md](RESULTS_AND_VALIDATION.md) | Verified findings and limitations |
| `outputs/focused_april/` | Focused notebook charts, result tables and cleaning audits |
| `outputs/full_period_eda/` | Broad EDA charts, audits, annual screening and April results |

Install [requirements.txt](requirements.txt), open the notebook in Jupyter, and select **Restart Kernel → Run All**.

Source: [CU-BEMS dataset paper](https://doi.org/10.1038/s41597-020-00582-3).
