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
   * Implements continuous min–max physiological normalization (`0.00–1.00`) across all individual environmental proxy layers.

3. **`03_Climate_Projections_and_Delta_Correction.ipynb`**
   * Interfaces directly with the Pangeo CMIP6 cloud catalog (`intake-esm`) to retrieve `MIROC6` (`r1i1p1f1`) ocean temperature (`tos`) projections (2025–2050) under `SSP2-4.5` and `SSP5-8.5`.
   * Computes historical SST delta bias-correction residuals (`SST_Bias_Correction_Residual.tif`) and projects mid-century peak DHW grids (`kaneohe_bay_MIROC6_DHW_SSP245_2025_2050.tif`).

4. **`04_Statistical_Validation_and_Figures.ipynb`**
   * Runs Pearson's Chi-Square ($\chi^2$) contingency tests (`scipy.stats.chi2_contingency`) and pairwise Odds Ratios ($\text{OR}$) comparing mid-century persistence across benthic cover classes ($N = 1,240,469$) and patchy versus dense coral frameworks ($N = 179,481$; $\text{OR} = 1.73, p < 0.001$).
   * Cross-tabulates persistent 2050 refugia across geomorphic zones and generates publication-grade figures.

## ArcGIS Pro Spatial Analytics & MCDA Workflow

Once the standardized, normalized GeoTIFF rasters were exported from Python, the final spatial decision modeling was executed in ArcGIS Pro:

* **Raster Calculator (Weighted Linear Combination):** Combines the normalized layers using Analytic Hierarchy Process (AHP) criteria weights ($\text{CR} = 0.0026$: Biological 54%, Physical 30%, Chemical 16%) to compute the continuous Restoration Suitability Index ($\text{RSI}$).
* **Reclassify & Conditional Map Algebra (`Con`):** Enforces the active viability threshold ($\text{RSI} \ge 0.60$), executes pixel-wise Limiting Factor Analysis to isolate the primary restrictive constraint, and allocates viable habitats across benthic classes into Master Intervention Codes 1–4.
* **Tabulate Area:** Extracts exact spatial footprints and hectare changes across geomorphic zones and ecological cover classes.

## Software Architecture & Spatial Standards
- **Spatial Reference System:** NAD83(PA11) / UTM Zone 4N (`EPSG:6634`)
- **Spatial Resolution:** 4.0-meter raster grids ($16\text{ m}^2$ per pixel)
- **Environment:** Python 3.10 with `xarray`, `rioxarray`, `rasterio`, `geopandas`, `numpy`, and `scipy`; ArcGIS Pro (Raster Calculator, Reclassify, Tabulate Area).

## Data Availability
Due to file size limits, large raster GeoTIFF outputs (`.tif`) and NetCDF climate arrays are archived in Google Drive (`/Capstone/01 Raw Data` through `06 Advanced Diagnostic & Delta Outputs`). To execute these notebooks, mount your Google Drive workspace matching the directory paths in Module 1–4.

---

## Poster Reference List

1. **Knowlton, N., et al. (2021).** *Rebuilding Coral Reefs: A Decadal Grand Challenge*. International Coral Reef Society and Future Earth Coasts. https://doi.org/10.53642/NRKY9386
2. **Ferrario, F., Beck, M. W., Storlazzi, C. D., Micheli, F., Shepard, C. C., & Airoldi, L. (2014).** The effectiveness of coral reefs for coastal hazard risk reduction and adaptation. *Nature Communications*, 5(1), 3794. https://doi.org/10.1038/s41586-014-3794-6
3. **Costanza, R., de Groot, R., Sutton, P., van der Ploeg, S., Anderson, S. J., Kubiszewski, I., Farber, S., & Turner, R. K. (2014).** Changes in the global value of ecosystem services. *Global Environmental Change*, 26, 152–158. https://doi.org/10.1016/j.gloenvcha.2014.04.002
4. **LaJeunesse, T. C., Parkinson, J. E., Gabrielson, P. W., Jeong, H. J., Reimer, J. D., Voolstra, C. R., & Santos, S. R. (2018).** Systematic revision of Symbiodiniaceae highlights the antiquity and diversity of coral endosymbionts. *Current Biology*, 28(16), 2570–2580.e6. https://doi.org/10.1016/j.cub.2018.07.008
5. **Hughes, T. P., et al. (2017).** Global warming and recurrent mass bleaching of corals. *Nature*, 543(7645), 373–377. https://doi.org/10.1038/nature21707
6. **Bayraktarov, E., Saunders, M. I., Abdullah, S., Mills, M., Beher, J., Possingham, H. P., Mumby, P. J., & Lovelock, C. E. (2016).** The cost and feasibility of marine coastal restoration. *Ecological Applications*, 26(4), 1055–1074. https://doi.org/10.1890/15-1077
7. **Boström-Einarsson, L., et al. (2020).** Coral restoration – A systematic review of current methods, successes, failures and future directions. *PLOS ONE*, 15(1), e0226631. https://doi.org/10.1371/journal.pone.0226631
8. **Anthony, K. R. N., et al. (2020).** Interventions to help coral reefs under global change—A complex decision challenge. *PLOS ONE*, 15(8), e0236399. https://doi.org/10.1371/journal.pone.0236399
9. **van Hooidonk, R., Maynard, J., Tamelander, J., Gove, J., Ahmadia, G., Raymundo, L., Williams, G., Heron, S. F., & Planes, S. (2016).** Local-scale projections of coral reef futures and implications of the Paris Agreement. *Scientific Reports*, 6(1), 39666. https://doi.org/10.1038/srep39666
10. **Malczewski, J. (2006).** GIS-based multicriteria decision analysis: A survey of the literature. *International Journal of Geographical Information Science*, 20(7), 703–726. https://doi.org/10.1080/13658810600661508
11. **Liu, G., et al. (2014).** Reef-scale thermal stress monitoring of coral ecosystems: New 5-km global products from NOAA Coral Reef Watch. *Remote Sensing*, 6(11), 11579–11606. https://doi.org/10.3390/rs61111579
12. **Eyring, V., Bony, S., Meehl, G. A., Senior, C. A., Stevens, B., Stouffer, R. J., & Taylor, K. E. (2016).** Overview of the Coupled Model Intercomparison Project Phase 6 (CMIP6) experimental design and organization. *Geoscientific Model Development*, 9(5), 1937–1958. https://doi.org/10.5194/gmd-9-1937-2016
13. **Kenyon, T. M., Doropoulos, C., Wolfe, K., Webb, G. E., Dove, S., Harris, D., & Mumby, P. J. (2023).** Coral rubble dynamics in the Anthropocene and implications for reef recovery. *Limnology and Oceanography*, 68(1), 110–147. https://doi.org/10.1002/lno.12254
14. **Ostrander, C. E., McManus, M. A., DeCarlo, E. H., & Mackenzie, F. T. (2008).** Temporal and spatial variability of freshwater plumes in a semienclosed estuarine–bay system. *Estuaries and Coasts*, 31(1), 192–203. https://doi.org/10.1007/s12237-007-9001-z
15. **Erftemeijer, P. L. A., Riegl, B., Hoeksema, B. W., & Todd, P. A. (2012).** Environmental impacts of dredging and other sediment disturbances on corals: A review. *Marine Pollution Bulletin*, 64(9), 1737–1765. https://doi.org/10.1016/j.marpolbul.2012.05.008
16. **Lowe, R. J., Falter, J. L., Monismith, S. G., & Atkinson, M. J. (2009).** Wave-driven circulation of a coastal reef–lagoon system. *Journal of Physical Oceanography*, 39(4), 873–893. https://doi.org/10.1175/2008JPO3958.1
17. **Saaty, T. L. (1980).** *The analytic hierarchy process: Planning, priority setting, resource allocation*. McGraw-Hill.
18. **Levy, N., Berman, O., Yuval, M., Loya, Y., Treibitz, T., Tarazi, E., & Levy, O. (2022).** Emerging 3D technologies for future reformation of coral reefs: Enhancing biodiversity using biomimetic structures based on designs by nature. *Science of the Total Environment*, 830, 154749. https://doi.org/10.1016/j.scitotenv.2022.154749
19. **Roepke, L. K., Brefeld, D., Soltmann, U., Randall, C. J., Negri, A. P., & Kunzmann, A. (2022).** Antifouling coatings can reduce algal growth while preserving coral settlement. *Scientific Reports*, 12(1), 15935. https://doi.org/10.1038/s41598-022-19997-6
20. **Baums, I. B., et al. (2019).** Considerations for maximizing the adaptive potential of restored coral populations in the western Atlantic. *Ecological Applications*, 29(8), e01978. https://doi.org/10.1002/eap.1978
21. **Glass, B. H., & Barott, K. L. (2025).** Hypoxia threatens coral and sea anemone early life stages. *Limnology and Oceanography*, 70(3), 684–699. https://doi.org/10.1002/lno.12798
22. **Barott, K. L., et al. (2021).** Coral bleaching response is unaltered following acclimatization to reefs with distinct environmental conditions. *Proceedings of the National Academy of Sciences*, 118(22), e2025435118. https://doi.org/10.1073/pnas.2025435118
23. **Gove, J. M., et al. (2023).** Coral reefs benefit from reduced land–sea impacts under ocean warming. *Nature*, 621(7979), 536–542. https://doi.org/10.1038/s41586-023-06394-w
