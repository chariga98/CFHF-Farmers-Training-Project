# CFHF: Agroecology Farmer Training Dashboard

> **Conservation Food & Health Foundation (CFHF) Project** — An R-based data pipeline and interactive Shiny dashboard for monitoring agroecology farmer training assessments across Kenya. This project was funded by CFHF grant for 2025/2026 year

---

## Table of Contents

- [Overview](#overview)
- [Project Structure](#project-structure)
- [Features](#features)
- [Data Sources](#data-sources)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Usage](#usage)
- [Dashboard Tabs](#dashboard-tabs)
- [Summary Tables](#summary-tables)
- [Map Visualization](#map-visualization)
- [County Coverage](#county-coverage)
- [Contributing](#contributing)

---

## Overview

This project provides a suite of R tools for analyzing and visualizing baseline and follow-up assessment data from the **CFHF Agroecology Farmer Training Programme**. It spans 5 workshop training days covering Soil Health, Pest Management, Nutrition & Value Addition, Livestock, and Climate & Agroforestry — tracking what practices farmers are applying across 13 counties in Kenya.

**Key outputs include:**
-  An interactive **Shiny Dashboard** for real-time, filter-driven exploration
-  A **Leaflet Map** showing farmer distribution by organization and county
-  Publication-quality **gt summary tables** for reporting
-  A unified **data pipeline** that stacks and cleans all practice datasets

---

## Project Structure

```
CFHF/
├── app.R                          # Shiny dashboard (main application)
├── data_pipeline.R                # Data loading, cleaning & stacking script
├── summary_tables.R               # Publication-quality gt tables
├── kenya_map.R                    # Leaflet interactive map
│
├── Data/
│   ├── CFHF.Data.Stacked.xlsx    # Primary stacked dataset (multi-sheet)
│   ├── CFHF.Data_Normal.xlsx     # Original wide-format data
│   ├── CFHF.Data_Classified.xlsx # Classified/categorized data
│   ├── CropsGrown.xlsx           # Crop adoption data (binary 0/1)
│   └── DIVFEEDCROP.xlsx          # Diversity & feed materials data
│
└── outputs/
    └── kenya_farmer_map.html     # Exported interactive map
```

---

## Features

| Feature | Description |
|---|---|
|  Cascading Filters | 7-level sidebar filters: Round → Workshop → Org → County → Sub-County → Cluster → Farmer |
|  Interactive Map | Leaflet map with clustered farmer markers, colored by organization |
|  Practice Charts | Vertical/horizontal bar charts and pie/doughnut charts per training day |
|  gt Tables | Color-coded crop adoption, diversity indicators, and practice tables by county/org |
|  Data Pipeline | Automated stacking of 25+ Excel sheets into a single tidy master dataset |
|  County Coverage | 13 Kenyan counties with sub-county resolution |

---

## Data Sources

All data originates from the **CFHF Project Baseline Evaluation** and is stored in Excel workbooks with one sheet per practice domain. The primary file `CFHF.Data.Stacked.xlsx` contains the following sheets:

| Sheet | Training Day | Domain |
|---|---|---|
| `Identifiers` | — | Farmer metadata |
| `Soil.Health.Practices` | Day 1 | Soil Health |
| `Pest.Management.Practices` | Day 2 | Pest Management |
| `Family.Nutrition`, `IncomeGen.Val.Addition` | Day 3 | Nutrition & Value Addition |
| `Go-Food`, `Grow-Food`, `Glow-Food` | Day 3 | Food Groups |
| `Sales.Ravenue.Tracking` | Day 3 | Sales |
| `LivestockPractices`, `Livestock.Kept`, `Feed.Materials`, `Livestock.Enterprises` | Day 4 | Livestock |
| `Agroforestry.FoodForest` ... `WaterTolerantCrop` | Day 5 | Climate & Agroforestry |

---

## Prerequisites

- **R** ≥ 4.1.0
- **RStudio** (recommended)

---

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/chariga98/cfhf-dashboard.git
cd cfhf-dashboard
```

### 2. Install R packages

Run the following in your R console:

```r
# Core packages
install.packages(c(
  "shiny", "shinydashboard", "bs4Dash", "shinyWidgets",
  "tidyverse", "readxl", "plotly", "DT", "purrr",
  "janitor", "scales"
))

# Mapping packages
install.packages(c(
  "leaflet", "sf", "rnaturalearth", "rnaturalearthdata"
))

# Optional: high-resolution country boundaries
remotes::install_github("ropensci/rnaturalearthhires")

# Table packages
install.packages(c("gt", "gtExtras", "geodata"))
```

### 3. Configure data paths

Update the file paths in each script to point to your local data files:

```r
# In app.R / data_pipeline.R
file_path <- "Data/CFHF.Data.Stacked.xlsx"

# In summary_tables.R
filepath  <- "Data/CropsGrown.xlsx"
filepath3 <- "Data/DIVFEEDCROP.xlsx"
filepath4 <- "Data/CFHF.Data_Tables.xlsx"
```

---

## Usage

### Run the Shiny Dashboard

```r
library(shiny)
shinyApp(ui, server)
# or
shiny::runApp("app.R")
```

### Run the Data Pipeline

```r
source("data_pipeline.R")
# Creates: master (tidy data frame with all practices + farmer identifiers)
```

### Generate Summary Tables

```r
source("summary_tables.R")
# Produces gt tables:
# - Crop adoption by county (≥50% threshold)
# - Diversity indicators by county
# - Practice adoption by organization (Days 1–5)
```

### Generate the Interactive Map

```r
source("kenya_map.R")
# Saves: outputs/kenya_farmer_map.html
```

---

## Dashboard Tabs

| Tab | Contents |
|---|---|
| **Overview** | KPI boxes (farmers, workshops, orgs, counties, sub-counties, clusters) + interactive Leaflet map |
| **Day 1: Soil Health** | Bar chart of soil fertility management practices |
| **Day 2: Pest Management** | Bar chart of integrated pest management practices |
| **Day 3: Nutrition & Value Addition** | Nutrition practices, value addition, Go/Grow/Glow food scatter plots, sales doughnut |
| **Day 4: Livestock** | Livestock practices, types kept, feed materials, enterprise pie chart |
| **Day 5: Climate & Agroforestry** | Agroforestry, appropriate tech, soil & water conservation, trees, medicinal plants, microgardens, green energy, drought/water-tolerant crops |

All charts display **"Farmers responded: X out of Y"** for response rate transparency.

---

## Summary Tables

Three types of publication-ready tables are generated using `{gt}` and `{gtExtras}`:

**1. Crop Adoption by County** — percentage of farmers growing each crop, color-coded:
- 🟣 Below 50% · 🟦 50–74% · 🟡 75–100%

**2. Diversity Indicators by County** — average number of livestock, food types, trees, etc. per farmer, color-coded:
- 🟣 0–2 · 🟦 3–5 · 🟡 6+

**3. Practice Adoption by Organization** — for each of the 5 training days, percentage of farmers per organization practicing each technique.

---

## Map Visualization

The interactive map (`kenya_map.R` / dashboard Overview tab) features:

-  **Clustered farmer markers** (auto-cluster at zoom-out, expand on zoom-in)
-  **Color-coded by organization** (SOFDI, KAPAP, KACE, FIPS, ANEW, CEFA, etc.)
-  **County and sub-county labels**
-  **Base map options**: CartoDB Positron, OpenStreetMap, Esri Satellite
-  **Popup details**: County, Organization per marker

---

## County Coverage

The project covers farmers across **13 Kenyan counties**:

`Vihiga` · `Kericho` · `Homa Bay` · `Uasin Gishu` · `Kakamega` · `Migori` · `Kisumu` · `Siaya` · `Busia` · `Trans Nzoia` · `Nandi` · `Kiambu` · `Bungoma`

---

## Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Commit your changes: `git commit -m "Add my feature"`
4. Push and open a Pull Request

---

*Source: CFHF Project Baseline Evaluation Data*
