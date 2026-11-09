# Kāneʻohe Bay Coral Restoration: Spatial Optimization & Climate Modeling

Welcome to the GitHub repository for my Master’s capstone thesis at the University of Pennsylvania. This repository houses the reproducible Python and spatial analysis pipeline used to model climate vulnerability, evaluate environmental bottlenecks, and optimize coral restoration strategies across Kāneʻohe Bay, Hawaiʻi.

## Repository Overview

The analysis is split into four modular, sequential Jupyter notebooks designed to run in Google Colab or a local Jupyter environment:

1. **`01_Data_Ingestion_and_Preprocessing.ipynb`**
   * Processes raw 4-meter NOAA/SOEST bathymetric LiDAR (`kaneohe_MB_Lidar_4m.asc` $\rightarrow$ `Kaneohe_Bathy_Final.tif`, `EPSG:6634`, `3853 × 3531` grid).
   * Clips and projects NOAA NCCOS benthic habitat vector surveys (`Kaneohe_Benthic.shp`) to the Kāneʻohe Bay study domain (`7,735.31 ha`).
   * Harvests multi-decade NOAA Coral Reef Watch v3.1 5-km SST and DHW NetCDF archives (2000–2025) and PacIOOS Clean Water Branch (CWB) water quality records.

2. **`02_MCDA_Spatial_Modeling.ipynb`**
   * Establishes the 4-meter master spatial grid lock from bathymetry (`5,159,407` valid marine pixels).
   * Filters PacIOOS CWB monitoring records (`n = 1,077` observations across 8 bay stations) to extract 5th-percentile storm salinity minima and 95th-percentile turbidity maxima, interpolating continuous 4-meter rasters via `scipy.spatial.cKDTree` Inverse Distance Weighting (IDW) and `scipy.ndimage.distance_transform_edt` runoff decay modeling.
   * Implements continuous min–max physiological normalization (`0.00–1.00`) and Analytic Hierarchy Process (AHP) weighted Multi-Criteria Decision Analysis (MCDA; $\text{CR} = 0.0026$: Biological 54%, Physical 30%, Chemical 16%).

3. **`03_Climate_Projections_and_Delta_Correction.ipynb`**
   * Interfaces directly with the Pangeo CMIP6 cloud catalog (`intake-esm`) to retrieve `MIROC6` (`r1i1p1f1`) ocean temperature (`tos`) projections (2025–2050) under `SSP2-4.5` and `SSP5-8.5`.
   * Computes historical SST delta bias-correction residuals (`SST_Bias_Correction_Residual.tif`) and projects mid-century peak DHW grids (`kaneohe_bay_MIROC6_DHW_SSP245_2025_2050.tif`).
   * Generates pixel-wise Limiting Factor Analysis rasters (`Limiting_Factor_Historical.tif`, `Limiting_Factor_Predictive.tif`) and Master Intervention transition grids (`Codes 1–4`, $\text{RSI} \ge 0.60$).

4. **`04_Statistical_Validation_and_Figures.ipynb`**
   * Runs Pearson's Chi-Square ($\chi^2$) contingency tests (`scipy.stats.chi2_contingency`) and pairwise Odds Ratios ($\text{OR}$) comparing mid-century persistence across benthic cover classes ($N = 1,240,469$) and patchy versus dense coral frameworks ($N = 179,481$; $\text{OR} = 1.73, p < 0.001$).
   * Cross-tabulates persistent 2050 refugia across geomorphic zones and generates publication-grade figures.

## Python vs. ArcGIS Pro Workflow

To ensure transparent and reproducible GIS modeling, the geoprocessing tasks are intentionally split between custom Python scripts and ArcGIS Pro spatial analytics:

* **Python (Preprocessing & Normalization):**
  * Handles raw data ingestion, raster clipping, and spatial grid-locking (`EPSG:6634`, 4-m resolution).
  * Executes `scipy.spatial.cKDTree` IDW interpolation for nearshore water quality stations ($n = 1,077$).
  * Applies continuous min–max physiological normalization ($0.00–1.00$) across all individual environmental proxy layers.

* **ArcGIS Pro (MCDA Overlay & Spatial Allocation):**
  * **Raster Calculator:** Executes Weighted Linear Combination (WLC) using AHP weights ($\text{CR} = 0.0026$: Biological 54%, Physical 30%, Chemical 16%) to produce the continuous Restoration Suitability Index ($\text{RSI}$).
  * **Reclassify & Conditional Map Algebra (`Con`):** Enforces the active viability threshold ($\text{RSI} \ge 0.60$), extracts pixel-wise Limiting Factors, and allocates viable habitats into Master Intervention Codes 1–4.
  * **Tabulate Area:** Extracts exact spatial footprints and hectare changes across geomorphic zones and benthic cover classes.

## Software Architecture & Spatial Standards
- **Spatial Reference System:** NAD83(PA11) / UTM Zone 4N (`EPSG:6634`)
- **Spatial Resolution:** 4.0-meter raster grids ($16\text{ m}^2$ per pixel)
- **Environment:** Python 3.10 with `xarray`, `rioxarray`, `rasterio`, `geopandas`, `numpy`, and `scipy`; ArcGIS Pro (Raster Calculator, Reclassify, Tabulate Area).

## Data Availability
Due to file size limits, large raster GeoTIFF outputs (`.tif`) and NetCDF climate arrays are archived in Google Drive (`/Capstone/01 Raw Data` through `06 Advanced Diagnostic & Delta Outputs`). To execute these notebooks, mount your Google Drive workspace matching the directory paths in Module 1–4.

---

## Poster Reference List

1. **Knowlton, N., et al. (2021).** *Rebuilding Coral Reefs: A Decadal Grand Challenge*. International Coral Reef Society and Future Earth Coasts. https://doi.org/10.53642/NRKY9386
2. **Ferrario, F., Beck, M. W., Storlazzi, C. D., Micheli, F., Shepard, C. C., & Airoldi, L. (2
