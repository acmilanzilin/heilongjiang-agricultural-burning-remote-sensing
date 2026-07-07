# Agricultural Burning Detection with Remote Sensing Data

[View the full rendered notebook](notebooks/agricultural_burning_remote_sensing.ipynb)

A geospatial Python project for identifying and characterising likely post-harvest agricultural burning patterns in Heilongjiang using satellite fire points, crop phenology, and administrative boundaries.

## Background

Crop residue burning is a persistent air-quality problem in agricultural regions. In China, maize and wheat stover can be burned after harvest because it is a quick way to clear fields before the next planting cycle. This practice can release particulate matter, carbon monoxide, nitrogen oxides and other pollutants, contributing to seasonal haze, visibility hazards and health risks.

Heilongjiang is used as the case study because it is one of China's major grain-producing provinces. Huang et al. (2026) mapped the main grain crops in Heilongjiang from 2019 to 2023 and showed clear spatial patterns of maize, rice and soybean across the province. This independent crop-mapping evidence supports the central assumption of this notebook: fire detections should be interpreted together with crop distribution and crop timing, rather than treated as agricultural burning by location alone.

## Research Questions

- What are the dominant seasonal and spatial patterns of fire activity in Heilongjiang?
- Which fire detections are likely to be post-harvest agricultural burning events?
- Did likely agricultural burning decline during 2010-2019?
- Do likely agricultural fires differ from other fires in fire radiative power (FRP)?

## Key Findings

- The cleaned MODIS dataset contains 1,073,117 fire detections across China from 2010 to 2019.
- After county-boundary filtering, 206,929 fire detections fall inside Heilongjiang.
- After NoData correction, 46,366 MODIS detections fall on valid maize/wheat maturity pixels; 4,088 pass the fixed post-maturity window screen.
- This subset should be interpreted as a lower-bound screen, not a comprehensive agricultural-burning detector.
- The classified agricultural-burning subset declines over time, with a fitted slope of -89.52 fires per year and a p-value of 0.0362.
- Post-maturity-screened fires have lower mean FRP than other fires: 11.31 compared with 19.10.
- During the FY validation period (August 2016-February 2017), 1,493 of 1,497 Heilongjiang FY points match same-period MODIS detections within 3 km and +/-1 day (99.73%), but only 19 of these matched points pass the current 30-day maize/wheat post-maturity screen (1.27%).

These results should be read as exploratory evidence, not final causal attribution. The workflow identifies a transparent and reproducible lower-bound subset by combining MODIS fire locations with valid maize/wheat maturity pixels and a post-maturity time window. The FY validation period confirms that many reference straw-burning points are visible in same-period MODIS data. The sensitivity analysis further shows that recall increases substantially when the post-maturity window is extended: the matched FY-MODIS recall rises from 1.27% under the 30-day window to 35.63% under the 90-day window. This suggests that the fixed 30-day window is one important reason for the low recall. However, recall remains incomplete even at 90 days. This is consistent with Zhou et al. (2021), who show that Heilongjiang straw resources include a large rice-straw component as well as maize straw, while this workflow only includes maize and wheat maturity rasters. Therefore, the current method should be interpreted as a conservative maize/wheat post-maturity screen, not a comprehensive straw-burning detector.

## Repository Contents

- `notebooks/agricultural_burning_remote_sensing.ipynb`: cleaned report-style analysis notebook.
- `data/sample/`: MODIS annual CSV files, crop maturity rasters, Fengyun straw-burning reference data used for validation, and smaller derived GeoJSON files.
- `data/external/`: full China county boundary shapefile and the full Heilongjiang MODIS GeoJSON.
- `outputs/agricultural_burning_remote_sensing.html`: rendered notebook report for quick viewing.

All available project data are retained in this directory, including the full `data/external/modis_hlj.geojson` file. Because this GeoJSON is larger than GitHub's normal 100 MB file limit, a public repository should use Git LFS for data files.

## Data Sources

### MODIS Active-Fire Detections

- Files: `data/sample/modis_2010_China.csv` to `data/sample/modis_2019_China.csv`
- Product type: satellite active-fire detections, approximately 1 km spatial resolution and four observations per day.
- Coverage: China, 2010-2019.
- Key fields used: latitude, longitude, acquisition date/time and fire radiative power (FRP).
- Role in analysis: main fire-event dataset; FRP is used as a proxy for fire intensity
- Field description reference: https://www.earthdata.nasa.gov/data/tools/firms/active-fire-data-attributes-modis-viirs

### Crop Maturity Rasters

- Files: `data/sample/Heilongjiang_Maize_MA_*.tif` and `data/sample/Heilongjiang_Wheat_MA_*.tif`
- Coverage: Heilongjiang, 2010-2019.
- Spatial resolution: 1 km.
- Key variable: crop maturity day of year (DOY). Raster values represent the DOY of maize or wheat maturity; NoData pixels indicate non-maize or non-wheat areas.
- Role in analysis: identifies whether a fire point falls on a valid maize or wheat maturity pixel, and whether the fire occurs shortly after the mapped maturity date.
- Reference: ChinaCropPhen1km, a high-resolution crop phenological dataset for three staple crops in China. https://essd.copernicus.org/articles/12/197/2020/

### Fengyun Straw-Burning Monitoring Data

- File: `data/sample/straw burning fire point monitoring data.xlsx`
- Coverage: August 2016 to February 2017
- Role in analysis: external reference dataset used to validate whether FY straw-burning points align with same-period MODIS detections and the current maize/wheat post-maturity screen.
- Limitations: the file does not identify crop type and does not cover the full 2010-2019 study period.
- Source: http://www.secmep.cn/ygyy/dqhjjc/

### Administrative Boundaries

- Files: `data/external/CHN_County.*`
- Role in analysis: filters national MODIS fire detections to Heilongjiang county-level boundaries
- Source: geoBoundaries administrative boundaries for China, hosted by the Humanitarian Data Exchange. https://data.humdata.org/dataset/geoboundaries-admin-boundaries-for-china

### References

- Huang, J., Wu, H., Zhang, X., Huang, J., Cheng, Y. and Wen, F. (2026). Mapping and spatiotemporal evolution of main grain crops in Heilongjiang Province from 2019 to 2023 based on spectral and temporal feature screening. *Remote Sensing for Natural Resources*, 38(1), 260-270. https://doi.org/10.6046/zrzyyg.2025024
- Zhou, Y., Liao, W., Yang, J., Huang, X. and Chen, C. (2021). Based on MODIS fire pixel location data to characterize the biomass burning and air pollutants emissions in northern and southern China. *Acta Scientiae Circumstantiae*, 41(9), 3696-3708. https://html.rhhz.net/hjkxxb/html/20200823001.htm

## How to Run

Open `notebooks/agricultural_burning_remote_sensing.ipynb` and run the cells from top to bottom from either the project root or the `notebooks/` folder. The notebook resolves paths through `data/sample/` and `data/external/` automatically.

With `CHN_County.shp` under `data/external/`, the notebook runs the full 2010-2019 boundary-filtered analysis. In the latest validation run, it identified 128 Heilongjiang county boundary rows, 206,929 Heilongjiang MODIS fire points, 46,366 detections on valid maize/wheat maturity pixels, and 4,088 post-maturity-screened detections.

## Dependencies

- `pandas`
- `geopandas`
- `numpy`
- `matplotlib`
- `seaborn`
- `rasterio`
- `shapely`
- `scipy`
