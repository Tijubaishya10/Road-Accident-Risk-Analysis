# Road Accident Risk Analysis & Hotspot Prediction: Silchar, Assam

A data-analytics workflow for ranking road-accident risk and predicting hotspots at city level, built for Silchar, Assam. It combines civil-engineering road-safety thinking (EPDO severity, junction and exposure attributes) with Python analytics.

> **Data disclaimer:** all crash records and the 1-5 location attribute scores are **synthetic** (random seed 42), generated to follow documented rules. The results demonstrate the **method**, not observed Silchar crash statistics. Real police / iRAD records can be dropped into the same pipeline.

## Why this project

Assam recorded 7,023 accidents (2,994 deaths) in 2022, 7,421 (3,296) in 2023 and 7,848 (3,351) in 2024 (Rajya Sabha Unstarred Question No. 2989, 18 Mar 2026). These are state totals and cannot be downscaled to a city, and cities like Silchar lack clean, geocoded crash databases. This project shows how locations can be ranked before problems repeat.

## Objectives

1. **Profile** temporal, vehicle and severity patterns of crashes
2. **Rank** locations by EPDO and a composite risk index
3. **Map** crash density and spatial hotspot clusters
4. **Predict** high-risk locations and months with ML, tested on unseen years

## Study area

14 locations across Silchar: Premtola, Fatak Bazar, Ambicapatty, Link Road, Tarapur, Rangirkhari, Railway Station, Meherpur, Malugram, NIT Point, Medical College (Ghungoor), Silchar-Kalain Road (Irongmara), Dorgakona, Sonai Road Jn. Each is scored 1-5 on junction complexity, traffic exposure, pedestrian exposure, lighting and speed concern.

## Dataset (`Silchar_Synthetic_Datasets.xlsx`)

| Sheet | Contents |
|---|---|
| `README` | How the data was generated |
| `Locations` | 14 locations with coordinates and 1-5 attribute scores |
| `Crashes` | 949 crash records: date, hour, vehicle, collision type, severity |
| `Monthly_Panel` | Location x month counts (2019-2024) used for prediction |
| `Assam_State_Totals` | Official state-level totals (2022-24) |

Monthly counts follow a Poisson process whose rate rises with junction complexity, exposure, pedestrians, poor lighting and speed, with a monsoon uplift (May-Sep), a mild yearly trend and a COVID dip (Apr-Jun 2020).

## Methodology

`Prepare` -> `Profile` -> `Screen (EPDO + composite index)` -> `Map (KDE + DBSCAN)` -> `Predict (Logistic, RF, GB; temporal split)`

- **EPDO weights (assumed):** Fatal 10, Grievous 4, Minor 2, Non-injury 1
- **Composite risk index:** `R = 0.30*C + 0.20*S + 0.15*E + 0.15*J + 0.10*V + 0.10*P` (crash frequency, severity, exposure, junction complexity, speed and lighting, pedestrian exposure); high tier is R above 55
- **Validation:** always on future years (train 2019-22, test 2023-24)

## Key findings (synthetic data)

- Crashes peak at 17:00-18:00; 22% occur at night; 45% fall in May-Sep (monsoon over-represented)
- Two-wheelers are involved in 45% of crashes; fatal + grievous crashes are about 24%
- Top-risk locations: Premtola, NIT Point, Tarapur, Medical College (Ghungoor); the densest zone is the Premtola-Fatak Bazar core
- Random Forest: F1 0.52, recall 0.61, ROC-AUC 0.68 for high-risk months; location ranking predicted with Spearman rho = 0.76 (4 of the top 5 correct)

Full results and charts are in the presentation.

## Repository contents

| File | Description |
|---|---|
| `Silchar_EDA.ipynb` | Data quality checks, temporal / vehicle / severity profiling, EPDO screening, exposure analysis, spatial first look |
| `Silchar_Synthetic_Datasets.xlsx` | The synthetic dataset |
| `Silchar_Road_Accident_Risk_Presentation final.pptx` | Project presentation |

## How to run

```bash
git clone https://github.com/Tijubaishya10/Road-Accident-Risk-Analysis.git
cd Road-Accident-Risk-Analysis
pip install pandas numpy matplotlib seaborn scipy openpyxl jupyter
jupyter notebook Silchar_EDA.ipynb
```

Keep the `.xlsx` in the same folder as the notebook. Figures are saved to a `figures/` folder.

## Engineering countermeasures to investigate

Junction channelisation and turning guidance; pedestrian crossings and refuges near markets, station and hospital; parking management; measured speed studies on Kalain and Sonai Road approaches; lighting, reflective signs and pavement/drainage repair; helmet enforcement and paved shoulders for two-wheelers.

## Limitations

- All crash and attribute data are synthetic; coordinates are approximate and 1-5 scores are assumed, not measured
- Index weights are provisional and not calibrated against real outcomes
- Models are tested on data from the same generator, so performance is likely optimistic
- Assam totals are state-level only

## Next steps

- Obtain 3-5 years of police / iRAD crash records with coordinates
- Run classified counts, turning counts and speed surveys
- Recalibrate weights; test negative-binomial safety performance functions and Getis-Ord Gi*
- Verify hotspots in the field

## Authors

Tiju Baishya and Astha Priyam Baruah, Department of Civil Engineering, NIT Silchar
