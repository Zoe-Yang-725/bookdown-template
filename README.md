
# Temperature Monitoring Project Documentation

***

<!-- badges: start -->

<img src="figures/logo.JPG" width="200" align="right">

<!-- badges: end -->

## Project Overview

This project focuses on temperature data monitoring, including dataset merging, data pre-processing, exploratory data analysis (EDA), and statistical process control (SPC) chart development.

At the current stage, the project is developed and illustrated using temperature data from the **Time in Motion** experiment.

The main goal is to detect abnormal temperature values in real-time by developing a distribution-driven exponentially weighted moving average (EWMA) control chart together with empirical-quantile-based one-sided extreme-limit control charts.

This documentation presents the full workflow, from raw data merging and pre-processing to EDA and SPC chart development, and provides clear and reproducible `R` scripts for the main analysis steps used in this project.

## Data Description

Two raw temperature files are used in this project:

- `Time_In_Motion_temperature_data.csv`: 

  - covers the time range from `2025-07-14 08:00:00` to `2025-08-01 07:59:00`

- `RR_Data_temperature_Aug_to_October.csv`: 

  - covers the time range from `2025-08-01 08:00:00` to `2025-10-03 09:46:00`

These two files are combined to create the full raw temperature dataset:

- `Time_in_Motion_temperature_complete.csv`:

  - covers the time range from `2025-07-14 08:00:00` to `2025-10-03 09:46:00`

After further cleaning and pre-processing for monitoring purposes, the final dataset used for temperature monitoring is:

- `Time_in_Motion_temperature_complete_clean.csv`:

  - covers the time range from `2025-07-14 08:00:00` to `2025-09-19 07:59:00`
  
## Phase I Baseline and Phase II Monitoring Periods 

- Historical baseline time range used for Phase I estimation:

  - from `2025-07-14 08:00` to `2025-09-10 23:59`

- Monitoring start time used for Phase II monitoring:

  - starting from `2025-09-18 00:00`

## Project Workflow

The project analysis is organised into the following steps:

*Step 1: Combine the raw data files*

- Script: `R/Combine Time in Motion Data into Full Dataset.R`

  - This `R` script combines the two raw temperature files into one complete **Time in Motion** temperature dataset.

*Step 2: Setup shared functions*

- Script: `R/Temperature data - Time in Motion  - Set up.R`

  - This `R` script contains the shared functions used throughout the project, including time zone set up, mapping, data loading, data pre-processing, feeding window removal, EDA summary helpers, 
plot helpers, distribution diagnosis, and monitoring-series preparation.

*Step 3: Exploratory data analysis*

- Script: `R/Temperature data - Time in Motion - EDA.R`

  - This `R` script performs EDA on the temperature data, including structural checks, duplicate checks, summary statistics, visualization of the data before and after feeding-window removal, 
and distribution exploration.

*Step 4: Data cleaning for monitoring*

- Script: `R/Temperature data - Time in Motion - Cleaning for Monitoring.R`

  - This `R` script cleans the full raw dataset for temperature data monitoring. Based on the EDA results, the later part of the **Time in Motion** experiment no longer reflects stable system operation, 
so additional data cleaning is applied before control chart development.

*Step 5: Control chart development*

- Script: `R/Temperature data - Time in Motion  - Control chart developement.R`

  - This `R` script develops the temperature monitoring framework, including monitoring-series construction, baseline estimation, EWMA control chart development, empirical one-sided extreme limit control chart development, 
flatline detection, and alarm outputs extraction.

## Repository Structure

The project repository is organised as follows:

```text
.
├── README.md
├── data/
│   ├── raw/
│   └── cleaned/
├── R/
├── docs/
├── output/
├── figures/
└── archive/
```

## Recommended Running Order

To reproduce the full workflow, run these `R` scripts in the following order:

- [ ] 1. `R/Combine Time in Motion Data into Full Dataset.R`
- [ ] 2. `R/Temperature data - Time in Motion - Set up.R`
- [ ] 3. `R/Temperature data - Time in Motion - EDA.R`
- [ ] 4. `R/Temperature data - Time in Motion - Cleaning for Monitoring.R`
- [ ] 5. `R/Temperature data - Time in Motion - Control chart development.R`

## Feeding Window Removal

The feeding window is defined as `08:00:00 am` to `12:00:00 pm` and is removed before control chart development in this project. 

## Monitoring Rules

The final monitoring framework combines the following rules:

- EWMA rule: Used to detect sustained shifts in the final chosen monitoring series over time. 

- Empirical-quantile-based one-sided extreme-limit rules: Used to detect unusual short-term high spikes and low dips in the raw temperature series.

- Flatline rule: 5 or more consecutive identical raw temperature readings 

## Packages Used

The main `R` packages used in this project are listed below:

``` r
# load packages we need 
library(ggplot2)
library(dplyr)
library(tidyr)
library(readr)
library(tidyverse)
library(ggpubr) 
library(zoo)
library(gridExtra)
library(plotly)
library(sjmisc)
library(tinytex)
library(knitr)
library(kableExtra)
library(splines)
library(anytime)
library(TTR)
library(spcadjust)
library(diptest)
library(lubridate)
```

## Documentation Files

The corresponding `R` Markdown files for each step of the above workflow are available in the `docs/` folder:

- `docs/index.html`
- `docs/shared-setup-and-helper-functions.html`
- `docs/eda-for-temperature-data.html`
- `docs/data-cleaning-for-monitoring.html`
- `docs/temperature-monitoring-control-chart-development.html`

## Notes

- Please open the project from `Temperature - Time in Motion.Rproj` before running the R scripts, so that all relative file paths work correctly.
- Some parameter settings may need to be adjusted depending on the experimental setting.
- The data cleaning and pre-processing rules used in this project are specific to the current **Time in Motion** experiment.

## Outputs

The code produces EDA summary tables, time series plots, distribution diagnostics, processed monitoring series, and control chart results. 

The main control chart output CSV files are saved and available in the `output/` folder:

- [ ] `baseline_stats_monitoring_series.csv`

  - This file contains baseline statistics the EWMA standard error, and the corresponding EWMA control limits (`CL`, `LCL`, and `UCL`) for the final chosen monitoring series. 
  These values are estimated from historical data in the Phase I baseline period and are used for Phase II EWMA monitoring.
  
- [ ] `baseline_extreme_limits_raw_grid.csv`

  - This file contains the empirical-quantile-based one-sided extreme limits calculated from the historical raw temperature data in the Phase I baseline period. 
  It contains the upper extreme limit based on block-wise raw maximum values (`UCL_raw_max`) and the lower extreme limit based on block-wise raw minimum values (`LCL_raw_min`). 
  These limits are used to detect short-term spikes and dips during Phase II monitoring.
  
- [ ] `mon_df_with_all_rules_summary.csv`

  - This file contains the full monitoring-grid results for all monitoring time points after combining all monitoring rules.
  
- [ ] `alarms_output_summary.csv`

  - This file contains only the monitoring time points where a signal is triggered, extracted from the full monitoring-grid results in `mon_df_with_all_rules_summary.csv`.

- [ ] `minute_raw_with_EWMA_ewma_limits.csv`

  - This file contains the minute-level raw temperature data matched with the monitoring-grid annotations and control chart results.
  
  
  
  
