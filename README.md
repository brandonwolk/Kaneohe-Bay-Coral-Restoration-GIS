# Kāneʻohe Bay Coral Restoration: Spatial Optimization & Climate Modeling

Welcome to the GitHub repository for my Master’s capstone thesis at the University of Pennsylvania. This repository houses the reproducible Python and spatial analysis pipeline used to model climate vulnerability, evaluate environmental bottlenecks, and optimize coral restoration strategies across Kāneʻohe Bay, Hawaiʻi.

## Repository Overview

The analysis is split into four modular, sequential Jupyter notebooks designed to run in Google Colab or a local Jupyter environment:

1. **`01_Data_Ingestion_and_Preprocessing.ipynb`**
   * Processes raw 4-meter NOAA LiDAR/multibeam bathymetry (`EPSG:6634`).
   * Clips NOAA benthic habitat vector surveys to the Kāneʻohe Bay study domain.
   * Automates ERDDAP data harvesting for NOAA Coral Reef Watch 5km SST/DHW grids and PacIOOS CWB water quality records.

2. **`02_MCDA_Spatial_Modeling.ipynb`**
   * Establishes the master spatial grid lock.
   * Executes Global Inverse Distance Weighting (IDW) via `cKDTree` to interpolate turbidity and salinity fields from PacIOOS stations.
   * Implements Van Hooidonk (2016) absolute DHW climate risk normalization and AHP-weighted multi-criteria decision analysis (MCDA).

3. **`03_Climate_Projections_and_Delta_Correction.ipynb`**
   * Interfaces directly with the Pangeo CMIP6 cloud catalog (`intake-esm`).
   * Subsets curvilinear MIROC6 GCM grids and computes SST/DHW delta bias corrections for mid-century (2025–2050) projections under SSP2-4.5 and SSP5-8.5.

4. **`04_Statistical_Validation_and_Figures.ipynb`**
   * Runs Chi-Square contingency tests and Odds Ratios comparing patchy versus dense coral frameworks.
   * Performs environmental gradient regressions along the bay's principal circulation axis.
   * Generates publication-grade figures for the manuscript.

## Software Architecture & Spatial Standards
* **Spatial Reference System:** NAD83(PA11) UTM Zone 4N (`EPSG:6634`)
* **Spatial Resolution:** 4.0-meter raster grids ($16\text{ m}^2$ per pixel)
* **Environment:** Python 3.x with `xarray`, `rioxarray`, `rasterio`, `geopandas`, and `scipy`.

## Data Availability
Due to file size limits, large raster GeoTIFF outputs (`.tif`) and NetCDF climate arrays are archived locally in Google Drive. To execute these notebooks, ensure your directory structure mirrors the script paths or connect a mounted Google Drive workspace containing your processed raster folders.
