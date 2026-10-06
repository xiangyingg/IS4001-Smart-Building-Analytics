# IS4001-Smart-Building-Analytics

An IS4001 project using the **CU-BEMS Smart Building Energy & Indoor Environmental dataset** to investigate building-performance problems and recommend measurable actions.

The project follows a common approach: define a business question, explore and clean the data, establish a fair baseline, and recommend an action linked to clear KPIs. Analyses aim to improve building performance while considering occupant comfort.

## Data scope

The local dataset covers **Floors 1–7 from July 2018 to December 2019**, including AC, lighting and plug-load power, indoor temperature, relative humidity and ambient light. Sensor availability varies by zone and period. Occupancy, outdoor weather and direct occupant feedback are not included.

## Business questions

| Analysis | Business question | Status |
|---|---|---|
| [Lighting and peak demand](SBA_Kaggle_Dataset/Light/README.md) | How much can peak electricity demand be reduced in each zone without compromising lighting comfort? | EDA and conditional scenarios completed; field validation required |

Additional business questions will be added as their scope and analysis are developed.

## Project structure

- `SBA_Kaggle_Dataset/data/` — original floor-level CSV files.
- `SBA_Kaggle_Dataset/Light/` — current lighting analysis, notebook, project scope, findings and generated outputs.

Each analysis documents its assumptions, data limitations, comparison method, recommended action and success metrics. Scenario estimates are distinguished from experimentally verified improvements.

Dataset reference: [CU-BEMS, Scientific Data (2020)](https://doi.org/10.1038/s41597-020-00582-3).
