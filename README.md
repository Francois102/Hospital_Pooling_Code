
```markdown
# ANALYSIS CODE DOCUMENTATION
# "Smoothing and Flattening the Curve: How Capacity Sharing between Hospitals Saves Lives during Pandemics"

---

## OVERVIEW

This folder contains all code necessary to reproduce the analyses in the paper *"Smoothing and Flattening the Curve: How Capacity Sharing between Hospitals Saves Lives during Pandemics"*.

The analysis examines how hospital service population size affects healthcare strain during COVID-19 through diversification mechanisms.

---

## SYSTEM REQUIREMENTS

- R version 4.0 or higher

---

## SETUP INSTRUCTIONS

### SET WORKING DIRECTORY
------------------------------
IMPORTANT: Set your working directory to the folder `/Replication_Code_Hospital_Pooling_Study` in the `00_master_analysis.R` script before running any code.

### DATA FILES
----------------------------

1. The following files are included in the `Data` folder:

   a) **COVID-19 Facility Data:**
   - File: `Data_Facility_TS`
   - Source: healthdata.gov, dataset: *COVID-19 Reported Patient Impact and Hospital Capacity by Facility*

   b) **County-level information:**
   - Source: American Community Survey (2015–2019)
   - File: `Data_County.xlsx`

   c) **Vaccination Rates:**
   - Source: CDC county-level vaccination data
   - File: `VaccinationRates.csv`

---

## RUNNING THE ANALYSIS

### RUN COMPLETE ANALYSIS
----------------------------------------------
```r
source("00_master_analysis.R")
```
This will:
- Install any missing R packages
- Import and process all data
- Run all analyses
- Generate all figures
- Save all results

---

## SCRIPT DESCRIPTIONS

### MAIN CONTROL SCRIPT
-------------------
- **00_master_analysis.R**  
  *Purpose:* Runs entire analysis pipeline in sequence

### DATA PREPARATION SCRIPTS
------------------------
- **01_load_libraries.R**  
  *Purpose:* Installs and loads all required R packages  
  *Packages:* nlme, lme4, quantregGrowth, ggplot2, dplyr, tidyr, etc.  
  *Note:* Will automatically install missing packages

- **02_import_data.R**  
  *Purpose:* Imports raw data and creates HSA-level datasets  
  *Output:* `HSA_df.txt` (main dataset), `ICU_occ.txt`, `Capacity.txt`

- **02a_wave_calculations.R**  
  *Purpose:* Helper functions for wave-specific calculations  
  *Called by:* `02_import_data.R`

### ANALYSIS SCRIPTS
----------------
- **03_regression_analysis.R**  
  *Purpose:* Mixed effects regression of ICU occupancy vs service population  
  *Method:* Linear mixed effects with heteroskedastic variance  
  *Output:* `Results/regression_models.RData`  
  *Paper:* Creates data for Figure 1 and Tables S1–S4

- **04_volatility_analysis.R**  
  *Purpose:* Analyzes volatility and power law relationships  
  *Method:* Log-log regression for power law fitting  
  *Output:* `Results/volatility_data.RData`  
  *Paper:* Creates data for Figure 2

- **05_temporal_clustering.R**  
  *Purpose:* Analyzes temporal clustering and pooling benefits  
  *Method:* Coefficient of variation analysis, simulation of pooling  
  *Output:* `Results/temporal_clustering_data.RData`  
  *Paper:* Creates data for Figure 3

- **05a_process_facilities.R**  
  *Purpose:* Helper functions for facility data processing  
  *Details:* Prepares facility-level hospital data for the temporal clustering and health effects analyses by calculating what proportion of each HSA's population each hospital serves

- **05b_clustering_functions.R**  
  *Purpose:* Functions for temporal clustering calculations

- **05c_hsa_example_plots.R**  
  *Purpose:* Creates example plots for specific HSAs

- **06_health_effects.R**  
  *Purpose:* Estimates mortality reduction from pooling  
  *Method:* Potential Impact Fraction calculation  
  *Output:* `Results/health_effects_data.RData`  
  *Paper:* Creates data for Figure 4

- **06a_health_effect_function.R**  
  *Purpose:* Helper functions for health effect calculations

### FIGURE GENERATION
-----------------
- **07_generate_figures.R**  
  *Purpose:* Creates all publication-ready figures  
  *Output:* PNG and SVG files in `/Figures` directory  
  *Note:* Must run all analysis scripts first

- **07a_figure_functions.R**  
  *Purpose:* Contains all plotting functions

---

## OUTPUT FILES

### FIGURES (in `/Figures` directory)
--------------------------------
- `Figure1_regression.png` / `Figure1_regression.svg` — Regression results (max ICU vs population)  
- `Figure2_volatility.png` / `Figure2_volatility.svg` — Volatility analysis and power laws  
- `Figure3_temporal_clustering.png` / `Figure3_temporal_clustering.svg` — Temporal clustering and pooling benefits  
- `Figure4_health_effects.png` / `Figure4_health_effects.svg` — Health effects heatmap  
- `FigureS1_regression_full.png` — Regression with all population sizes  
- `FigureS2_sample_counts.png` — Sample sizes for health effects

### RESULTS (in `/Results` directory)
--------------------------------
- `regression_models.RData` — Fitted regression model objects  
- `volatility_data.RData` — Volatility analysis results  
- `temporal_clustering_data.RData` — Clustering analysis results  
- `health_effects_data.RData` — Health impact estimates

### DATA FILES (in `Intermediate_outputs` directory)
----------------------------------
- `HSA_df.txt` — Main HSA-level dataset (see Variable Dictionary below)  
- `Hosp_demand` — Time series of hospital admissions per 100,000 population at the HSA level  
- `ICU_demand` — Time series of cumulative weekly ICU patient-days at the HSA level (e.g., 3 patients over 7 days = 21 patient-days)  
- `ICU_occ` — Time series of weekly average ICU occupancy (fraction) at the HSA level  
- `Capacity` — Time series of weekly cumulative ICU beds available at the HSA level (e.g., 2 beds over 7 days = 14 bed-days)

---

## VARIABLE DICTIONARY — `HSA_df` DATASET

### DEMOGRAPHICS & GEOGRAPHY
- **HSA:** Health Service Area identifier number  
- **Population:** Total population of HSA  
- **facilities:** Number of healthcare facilities in HSA  
- **Income:** Average income per capita ($)  
- **Area:** Total square miles  
- **Density:** Population density (people per square mile)  
- **Over60:** Proportion of population aged 60+

### MAXIMUM ICU OCCUPANCY (% of capacity)
- **max_est_w2:** Maximum during Winter 2020 (weekly)  
- **max_est_Delta:** Maximum during Delta (weekly)  
- **max_est_Omicron:** Maximum during Omicron (weekly)

### ICU CAPACITY (beds per 100,000 population)
- **Capw2:** Capacity at Winter 2020 peak  
- **CapDelta:** Capacity at Delta peak  
- **CapOmicron:** Capacity at Omicron peak

### PEAK TIMING
- **Peak_w2:** Date of Winter 2020 peak (YYYY-MM-DD)  
- **Peak_Delta:** Date of Delta peak (YYYY-MM-DD)  
- **Peak_Omicron:** Date of Omicron peak (YYYY-MM-DD)

### CUMULATIVE HOSPITALIZATIONS (per 100,000)
- **Cumul_w2:** During Winter 2020 wave  
- **Cumul_Delta:** During Delta wave  
- **Cumul_Omicron:** During Omicron wave  
- **Cumul_w2_4w:** 4 weeks around Winter 2020 peak  
- **Cumul_Delta_4w:** 4 weeks around Delta peak  
- **Cumul_Omicron_4w:** 4 weeks around Omicron peak

### VOLATILITY MEASURES
- **stdv_ICU_w2:** ICU volatility during Winter 2020  
- **stdv_ICU_Delta:** ICU volatility during Delta  
- **stdv_ICU_Omicron:** ICU volatility during Omicron  
- **jump_wave2:** Max week-to-week change Winter 2020  
- **jump_delta:** Max week-to-week change Delta  
- **jump_omicron:** Max week-to-week change Omicron

### VACCINATION RATES (%)
- **Vaccination_Delta:** % with ≥1 dose at Delta peak  
- **Vaccination_Omicron:** % with ≥1 dose at Omicron peak  
- **Avg_Vaccination_Delta_Period:** Average during Delta wave
```



