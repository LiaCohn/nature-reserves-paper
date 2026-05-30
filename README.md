# The Effect of Heatwaves on Visits to National Parks and Reserves

**Authors:** Aviv Sar-Shalom & Liav Cohn  
**Published in:** [PLOS ONE, August 2023](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0289201)  
**Supervisors:** Dr. Zohar Barnett-Itzhaki, Dr. Lior Chen, Dr. Ofer Steinitz

---

## Overview

This project examines how climate-change-related events — heatwaves and air pollution (PM10) — affect the number of visits to national parks and nature reserves in Israel. Using a dataset of 68,518 visitor entries across 51 sites from 2016–2019, we crossed park entrance data with meteorological and air quality data and analyzed the results using statistical tests and predictive models.

The study found that heatwaves and PM10 exceedances are both significantly associated with fewer visits to parks — and that sites with water sources (beaches, rivers, lakes) are partially shielded from the heatwave effect.

---

## Repository Structure

```
.
├── VisitorEntrancesV3.xlsx                          # Main dataset (68,518 entries, 51 sites, 31 features)
├── Temperature-2016-2020-Byte-Dagan.csv             # Daily max temperature from Israel Meteorological Service
├── function.py                                      # Shared utility functions
│
├── # --- Data Cleaning & Feature Engineering ---
├── Edit-Temperature-and-Heatwaves.ipynb             # Merge temperature data; compute heatwave flags
├── remove-unnecessary-features.ipynb                # Consolidate holiday/heritage flags; drop redundant columns
├── remove-unnecessary-Sites.ipynb                   # Remove 5 low-traffic sites with inconsistent patterns
├── remove-2020-records.ipynb                        # Remove COVID-19 era records
├── remove-operations-records.ipynb                  # Handle military operation days
│
├── # --- Exploratory Analysis ---
├── Correlation.ipynb                                # Feature correlation matrix; drop highly correlated features
├── sites map/                                       # Interactive HTML map of all 51 sites with icons by type
│
├── # --- Statistical Tests ---
├── Statistics-Temperature-WhenPrevWeekIsHeatwave.ipynb   # Visits in weeks before a heatwave
├── Statistics-Temperature-WhenNextWeekIsHeatwave.ipynb   # Visits in weeks after a heatwave
├── Statistics-CitesPollution-WhenNextDayIsRegular.ipynb  # Visits on polluted day vs. following clean day
├── Statistics-CitesPollution-WhenDayBeforeIsRegular.ipynb # Visits on clean day vs. following polluted day
├── Statistics-SitePollution-WhenNextDayIsRegular.ipynb   # Same tests using site-level PM10 (not national)
├── Statistics-SitePollution-WhenDayBeforeIsRegular.ipynb
│
├── # --- Models ---
├── Model MLR/                                       # Multiple Linear Regression per site
│   ├── MLR-Banias.ipynb
│   ├── MLR-Caesarea.ipynb
│   ├── MLR-En-Gedi.ipynb
│   └── MLR-Masada.ipynb
│
└── Model NN/                                        # Neural Network models per site (with/without last-year visits)
    ├── NN-Banias.ipynb
    ├── NN-Ceasarea.ipynb
    ├── NN-En-Gedi.ipynb
    ├── NN - The Masada.ipynb
    ├── NN-Banias without last year.ipynb            # Ablation: remove last_year_visitors feature
    ├── NN-Ceasarea without last year.ipynb
    ├── NN-En-Gedi without last year.ipynb
    ├── NN - The Masada without last year.ipynb
    └── weights*.h5                                  # Saved model weights
```

---

## Dataset

The main dataset (`VisitorEntrancesV3.xlsx`) contains one row per site per day and includes the following features:

| Category | Features |
|---|---|
| Identity | `Date`, `Site_Name` |
| Target | `Israelis_Count` |
| Geography | `region_Central`, `region_North`, `region_South`, `region_Judea_Samaria` |
| Site type | `is_nationalPark`, `is_natureReserve`, `is_water`, `is_cave`, `is_archaeology`, `is_animals`, `is_lookout` |
| Time | `Season_*`, `is_weekend`, `IsHoliday`, `IsVacation`, `IsHeritage` |
| Climate | `Temperature`, `isHeatwave` |
| Air quality | `Exceeded_pm10_in_Israel` (national), `Site_Exceeded` (local), `pm10` |
| Historical | `last_year_visitors` |
| Other | `is_camping`, `special_activity`, `visit_duration` |

**Key methodological note on PM10:** Air pollution was measured at monitoring stations near Israel's major population centers (covering ~51% of the population), intentionally representing the *departure location* of visitors — not the parks themselves. This design choice allowed us to test whether people's *local* air quality perception drives their decision to visit, regardless of conditions at the destination.

---

## Methods

### Heatwave Definition
A day is flagged as a heatwave day if it belongs to a streak of **at least 3 consecutive days** with a maximum temperature ≥ 32.2°C at the Bet Dagan station — the standard used by the Israel Meteorological Service and Ministry of Health.

### Statistical Tests
All tests used **nonparametric methods** (data was not normally distributed) and excluded weekends, holidays, and vacation days to avoid confounding.

- **Wilcoxon one-sided paired tests** — compared visits on polluted/heatwave days vs. adjacent clean days at the same site
- **Pearson & Spearman correlations** — temperature vs. visits, by region and season
- **Fisher's exact test** — association between heatwave days and PM10 exceedances; association between water-source sites and visit increases during heatwaves
- **FDR correction** — applied for multiple comparisons across sites

### Models
Linear and quadratic **Multiple Linear Regression (MLR)** and **Neural Networks (NN)** were trained on 4 representative sites. Features were MinMax normalized. Models were evaluated using RMSE and residual plots. **LIME** was used to explain individual NN predictions.

---

## Key Findings

- Heatwave weeks had **18.3% fewer visits** on average vs. non-heatwave weeks (p < 0.001)
- Sites **with water sources** (beaches, rivers) showed the *opposite* pattern — more visits during heatwaves (Fisher p < 0.001)
- **National PM10 exceedances** (city-level pollution) were significantly associated with fewer visits across all regions — even though pollution was measured far from the parks
- Heatwaves and PM10 exceedances are themselves **correlated** (Fisher p < 0.001), suggesting the air pollution effect on visits may be partially mediated by heatwaves
- Visit counts as a function of temperature follow a **unimodal (inverted-U) distribution** — peaking at mild temperatures and dropping on both cold and very hot days

---

## Dependencies

```
pandas
numpy
scipy
scikit-learn
tensorflow / keras
matplotlib
lime-stability
pandasql
openpyxl
```

---

## Citation

> Barnett-Itzhaki Z, Sar-Shalom A, Cohn L, Chen L, Steinitz O (2023). *The effect of heatwaves on the number of visits to national parks and reserves.* PLoS ONE 18(8): e0289201. https://doi.org/10.1371/journal.pone.0289201
