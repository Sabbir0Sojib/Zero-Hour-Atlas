# Zero Hour Atlas
### Satellite Derived Mapping of Early Warning Last Mile Reach Gaps
### Jamuna Padma Char Corridor, Bangladesh

![Python](https://img.shields.io/badge/Python 3.10 blue)
![Google Earth Engine](https://img.shields.io/badge/GEE Sentinel  1 green)
![RIMES Mapathon](https://img.shields.io/badge/RIMES Mapathon%202026 orange)
![License](https://img.shields.io/badge/License MIT lightgrey)

   

## Overview

Bangladesh is among the most flood exposed nations on Earth, with approximately 21% of its territory inundated annually during the monsoon season. Within this broader landscape of hydrological risk, the riverine char islands of the Jamuna corridor represent the most acutely exposed human settlements on the planet. Chars are ephemeral alluvial landforms low lying, unstable, and entirely surrounded by dynamic braided channels that support an estimated 2.8 million residents across Sirajganj, Pabna, Tangail, and adjacent districts. These communities lie outside the protection of embankments, lack all weather road connectivity, and are systematically excluded from the reach of formal early warning systems (EWS). The Regional Integrated Multi Hazard Early Warning System (RIMES) Impact Based Forecasting (IBF) 2025 initiative and the United Nations Early Warnings for All (EW4All) roadmap have identified last mile delivery failure as the defining bottleneck in reducing flood mortality in South and Southeast Asia, and the chars of the Jamuna Padma corridor exemplify this failure at its most extreme.

The Zero Hour concept captures a compounding infrastructure failure mode that is distinct from and more dangerous than forecasting failure. Zero Hour occurs when, simultaneously, a flood event peaks, road access fails due to inundation, the nearest mobile communication tower is submerged, and the community's designated emergency shelter is either flooded or unreachable within safe travel time. Under these conditions, a community may have received an accurate forecast hours or days in advance and yet remain unable to act upon it because every physical and digital channel linking that community to warning dissemination infrastructure has collapsed at the same moment. This is a systemic infrastructure failure, not a predictive failure. The Zero Hour Atlas project was designed to map the spatial distribution and intensity of this compound failure condition across the entire Kazipur–Doulatdiya reach of the Padma corridor.

The Zero Hour Index (ZHI) is a composite geospatial metric constructed from three sub indices: the Physical Hazard Index (PHI), the Access and Network Index (ANI), and the Shelter Reachability Index (SRI). Each sub index is independently calculated at 59 settlement analysis points distributed across a 1,225.2 km² study area and rescaled to a 0–100 range before being combined using a weighted sum formulation. The resulting ZHI score is classified into five ordered categories ranging from EWS Connected (ZHI < 15) through Critical Zero Hour (ZHI ≥ 75), providing a structured basis for prioritisation. Approximately 160 or more Sentinel 1 Ground Range Detected (GRD) SAR images acquired via Google Earth Engine (GEE) between 2017 and 2024 underpin the flood frequency component of the analysis, and 25 mobile communication towers sourced from OpenCelliD were inventoried and individually assessed for flood exposure. The combined result is a fully reproducible, open data pipeline that generates both cartographic outputs and quantitative settlement rankings.

This project was developed as a contribution to the RIMES Mapathon 2026, Theme 01: Geospatial Data for Disaster Risk Assessment. The entire analytical pipeline is implemented using openly licensed tools Python 3.10, Google Earth Engine, QGIS, and a suite of geospatial Python libraries and all input datasets are drawn from freely accessible global repositories including Sentinel 1 GRD SAR, Sentinel 2 multispectral imagery, SRTM 30 m DEM, OpenStreetMap, OpenCelliD, and GADM v4.1. The methodology is designed to be transferable to any riverine delta environment with comparable data availability, and the GEE scripts are structured for straightforward annual update and recalculation.

   

## Key Findings

### Table 1  Summary Statistics

| Metric | Value |
|        |       |
| Study area | 1,225.2 km² |
| Analysis points | 59 |
| Critical Zero Hour settlements (ZHI ≥ 75) | 11 of 16 |
| Total mobile towers inventoried | 25 |
| Flood risk towers (40% of total) | 10 |
| Dead zone points (>5 km from any tower) | 25 |
| Total erosion 2018–2025 | 43.5 km² |
| Total accretion 2018–2025 | 11.4 km² |
| Net land loss 2018–2025 | −32.1 km² |
| Erosion rate acceleration (2018 → 2025) | 7.7× (1.3 → 9.8 km²/yr) |
| Average days isolated per year (critical points) | 347 |
| Sentinel 1 images processed | ~160+ (2017–2024) |

### Table 2 Settlement ZHI Scores

| Settlement | ZHI Score | Class | Days Isolated/yr |
|   |   |   |   |
| Char Uttar Pata | 85.29 | Critical Zero Hour | 347 |
| Char Nasipur | 84.76 | Critical Zero Hour | 356 |
| Sujanagar | 83.35 | Critical Zero Hour | 362 |
| Kazipur | 82.91 | Critical Zero Hour | 347 |
| Char Shyampur | 82.82 | Critical Zero Hour | 347 |
| Char Bhatpara | 81.09 | Critical Zero Hour | 350 |
| Char Mirjapur | 80.71 | Critical Zero Hour | 347 |
| Enayetpur | 80.59 | Critical Zero Hour | 351 |
| Char Hurasagar | 78.55 | Critical Zero Hour | 347 |
| Ullapara | 78.54 | Critical Zero Hour | 346 |
| Chowhali | 75.87 | Critical Zero Hour | 347 |
| Sirajganj Town | 61.04 | Severe Isolation | 250 |

   

## Methodology

### Zero Hour Index Formula

The ZHI is calculated at each of the 59 analysis points using the following weighted composite:

```
ZHI = 0.35 × PHI + 0.35 × ANI + 0.30 × SRI
```

Each sub index is independently normalised to the 0–100 range before aggregation. The weights reflect expert elicitation conducted during project design, balancing the physical hazard dimension equally with the network access dimension while giving slightly lower weight to shelter reachability, which is partly captured within the ANI component.

### ZHI Classification Table

| Class | ZHI Range | Map Colour |
|       |           |            |
| EWS Connected | < 15 | Navy blue |
| Partial EWS | 15–35 | Light blue |
| Moderate Gap | 35–55 | Yellow/gold |
| Severe Isolation | 55–75 | Orange |
| Critical Zero Hour | ≥ 75 | Red |

   

### Physical Hazard Index (PHI)

The Physical Hazard Index quantifies the intensity and frequency of flooding experienced at each analysis point, integrating three dimensions of flood exposure: inundation frequency derived from multi year SAR analysis, relative elevation above the local floodplain, and proximity to the active braid channels of the Jamuna and Padma rivers. The primary data source is a stack of approximately 160 Sentinel 1 GRD SAR images acquired through Google Earth Engine over the 2017–2024 period, processed using a threshold based water detection algorithm applied to the VV polarisation band. Flood frequency is expressed as the proportion of wet season observations in which a given point is classified as inundated, and this frequency surface forms the dominant input to the PHI.

Elevation data from the SRTM 30 m DEM is used as a secondary input to the PHI, with low elevation areas (below 8 m above sea level within the delta context) assigned disproportionately higher hazard scores. Channel proximity is derived from the OSM river network and is weighted to capture the additional erosion and rapid inundation risk associated with settlements adjacent to active channels. The three sub components are combined using equal weights within the PHI, and the resulting index is rescaled to 0–100 before entry into the ZHI formula. PHI values in the study area range from approximately 30 at better protected upland margins to above 90 at mid channel chars with annual inundation exceeding 60% of wet season observations.

### Access and Network Index (ANI)

The Access and Network Index captures the compound failure of road accessibility and mobile communication connectivity at each analysis point during a flood event. The road component is derived from OpenStreetMap and uses a flood adjusted accessibility model in which road segments are progressively degraded or removed as flood depth increases, with earthen roads and unclassified paths receiving the highest vulnerability weight. The result is a measure of road isolation days per year the number of days on which a settlement cannot be reached by standard surface transport which forms one of two equal weight inputs to the ANI.

The mobile tower component draws on the OpenCelliD point dataset of 2G, 3G, and 4G towers inventoried across the study area, with each tower assessed for its individual flood exposure using the same Sentinel 1 derived flood frequency raster employed in the PHI. A tower is classified as flood risk if its location falls within a zone with inundation frequency above 20% in the wet season. The ANI then quantifies, for each analysis point, both whether the nearest tower is flood risk and whether the point falls within a 5 km service radius of any non flooded tower. Points beyond 5 km from all towers of which 25 are identified across the study area receive maximum ANI scores reflecting complete network isolation. ANI is the second highest weighted component of the ZHI, reflecting the centrality of communication failure to the Zero Hour failure mode.

### Shelter Reachability Index (SRI)

The Shelter Reachability Index measures the degree to which each analysis point can reach a designated emergency cyclone or flood shelter within a safe and actionable travel time under flood conditions. Shelter locations are sourced from OpenStreetMap and supplemented with BDRCS (Bangladesh Red Crescent Society) facility data where available. Travel time is modelled using a flood modified cost surface derived from the OSM road network, applying higher movement costs to inundated or low elevation paths and assuming that non motorised flood season transport (country boats) operates at a fraction of the speed of road based transport.

A shelter is considered reachable under the SRI framework if it can be accessed within 90 minutes under peak flood conditions using the best available transport mode. Shelters are additionally assessed for their own flood exposure: a shelter that is itself inundated or at risk of inundation contributes zero reachability benefit to surrounding settlements. SRI scores range from 0 (shelter within 30 minutes, not flood risk) to 100 (no reachable shelter within 90 minutes under flood conditions). Several critical char settlements in the study area score above 80 on the SRI alone, reflecting the near total absence of accessible, safe shelter infrastructure at peak monsoon.

   

### Data Sources

| Source | Variable | Resolution | Period |
|   |   |   |   |
| Sentinel 1 GRD SAR (GEE) | Flood frequency | 10 m | 2017–2024 |
| Sentinel 2 NDWI (GEE) | Char area change | 10 m | 2017–2025 |
| SRTM DEM | Elevation risk | 30 m | 2000 |
| OpenStreetMap | Roads + shelters | Vector | 2024 |
| OpenCelliD | Mobile tower locations | Point | 2025 |
| GADM v4.1 | Administrative boundaries | Vector | 2024 |
| CHIRPS | Precipitation | 5.5 km | 2017–2025 |

   

## Maps and Outputs

All cartographic outputs were produced in QGIS 3.x and are stored in the `maps/` and `charts/` directories of this repository. Each file is described below with reference to its actual visual content.

| File | Description |
|      |             |
| `MAP01_FloodHazard.png` | ZHI results map: flood hazard raster background (5 class colour ramp: blue = low, yellow/gold = moderate, orange = high, red = very high) with green settlement analysis points (59 total) and triangular tower symbols (10 red = flood risk, 15 blue = safe). Red dashed line = study area boundary. Inset: Bangladesh location map. Data: Sentinel 1 GRD GEE, Sentinel 2 NDWI, SRTM 30 m, OSM, OpenCelliD. Projection: EPSG:3857. |
| `MAP02_ZeroHour.png` | ZHI bubble composite map: circular bubble markers scaled proportionally to ZHI score and days isolated per year, overlaid on ZHI classification raster background (blue/yellow/gold tones). Red triangles = flood risk towers, blue triangles = safe towers, white/grey circles = no signal analysis points. Red dashed study boundary. Inset: Bangladesh location map. Data: Sentinel 1 GEE, OSM, OpenCelliD, GADM v4.1, SRTM 30 m. |
| `MAP06_TowerVulnerability.png` | Mobile tower flood vulnerability map: flood hazard raster background (same 5 class blue→red colour ramp). Towers labelled by generation (2G/3G/4G). White triangles = safe towers, red X markers = flood risk towers. Green dots = settlement coverage analysis points. Summary inset box: Total towers: 25, Flood risk: 10, Points >5 km from any tower: 25. Data: OpenCelliD, Sentinel 1 flood risk, OSM, GADM v4.1, SRTM 30 m. |
| `CHART4_ErosionAccretion.png` | Annual erosion vs accretion diverging horizontal bar chart. Title: "Annual Erosion vs Accretion Balance · Jamuna Padma Char Corridor · vs 2017 Baseline." Red bars extending left = erosion (land lost in km²); green bars extending right = accretion (land gained in km²). Years 2018–2025. Erosion values: 1.3, 2.7, 3.2, 4.7, 6.1, 7.0, 8.6, 9.8 km². Accretion values: 0.5, 0.7, 1.0, 1.3, 1.5, 1.9, 2.3, 2.3 km². Source: Sentinel 2 NDWI change detection via GEE, dry season composites. |
| `CHART4_CumLandChange.png` | Cumulative land change summary graphic. Title: "Cumulative Land Change · 2018–2025" with navy header bar. Three summary boxes: red box = 43.5 km² Total Erosion; green box = 11.4 km² Total Accretion; dark red box = −32.1 km² Net Land Change. Subtitle: "Sentinel 2 NDWI · vs 2017 Baseline · Jamuna Padma Char Corridor." |

### Detailed Map Notes

**MAP01 ZHI Results Map:** This is the primary results map of the project, displaying the spatial distribution of the five class ZHI classification across the study area. The raster background encodes flood hazard intensity from the Sentinel 1 derived flood frequency analysis, with cooler blue tones indicating lower hazard and red indicating very high hazard. The 59 green settlement analysis points represent the locations at which ZHI scores were calculated, and the triangular tower symbols provide immediate visual identification of which towers lie in flood risk zones. The study area boundary (red dashed line) corresponds to the Kazipur–Doulatdiya administrative corridor. The inset map situates the study area within Bangladesh for geographic context. Projection is EPSG:3857 (Web Mercator).

**MAP02 ZHI Bubble Composite:** This map communicates the magnitude of ZHI scores and isolation durations simultaneously through scaled circular bubble markers. Larger bubbles indicate higher ZHI scores and longer estimated isolation periods, enabling viewers to rapidly identify the most severely affected settlements. The raster background shows ZHI classification colours (blue for EWS Connected, graduating through yellow, gold, and orange to red for Critical Zero Hour). White or grey unfilled circles denote analysis points that fall outside the service range of any mobile tower (dead zones). The combination of bubble size, fill colour, and background raster provides a multi dimensional view of the compound failure condition.

**MAP06 Mobile Tower Vulnerability:** This map focuses specifically on the telecommunications infrastructure failure dimension of the Zero Hour framework. Towers are individually labelled by their network generation (2G, 3G, or 4G) to convey both connectivity capacity and likely ownership structure. The distinction between white triangle (safe) and red X (flood risk) symbols provides an immediate assessment of which towers are likely to fail simultaneously with a flood event. The summary inset box showing 25 total towers, 10 flood risk, and 25 dead zone points allows readers to grasp the coverage deficit without detailed map reading.

   

## How to Reproduce

Follow these steps to reproduce the Zero Hour Atlas analysis from scratch using the open data pipeline.

**Step 1 Clone the repository:**

```bash
git clone https://github.com/[username]/zero hour atlas
cd zero hour atlas
```

**Step 2 Install Python dependencies:**

```bash
pip install  r requirements.txt
```

The `requirements.txt` file includes: `earthengine api`, `geemap`, `geopandas`, `rasterio`, `fiona`, `shapely`, `matplotlib`, `seaborn`, `pandas`, `numpy`, `scipy`, `jupyter`, and `folium`.

**Step 3 Authenticate with Google Earth Engine:**

```bash
earthengine authenticate
```

You will need a valid Google Earth Engine account. Authentication opens a browser window for OAuth confirmation. After completing authentication, the credentials are stored locally and used automatically by the analysis notebook.

**Step 4 Launch the analysis notebook:**

```bash
jupyter notebook Zero_Hour_Atlas_Analysis.ipynb
```

The notebook is structured into sequential sections corresponding to each sub index (PHI, ANI, SRI), the ZHI composite calculation, and the cartographic output generation. Each cell includes inline comments documenting the logic and data transformations applied.

**Step 5 Input data:**

All input data files are stored in the `data/` directory and are either included directly in the repository (vector and tabular data) or downloaded automatically by the notebook from public APIs (Sentinel 1, Sentinel 2 via GEE; OSM via Overpass API). Ensure that the following files are present before running the notebook:

```
data/study_boundary.geojson
data/settlements_indexed.csv
data/towers.csv
```

**Step 6 Review outputs:**

Completed map outputs are written to the `maps/` directory and chart outputs to the `charts/` directory. The notebook's final cells export both PNG and GeoTIFF versions of the raster outputs. Scored settlement data is written to `data/settlements_scored.csv` and summary statistics to `data/summary_statistics.csv`.

   

## Limitations

**Mobile tower inventory completeness.** The OpenCelliD dataset is crowdsourced and subject to variable reporting density across different administrative areas. In remote char regions with limited smartphone penetration, tower records may be underreported, meaning that the 25 towers inventoried in this study may represent a minimum rather than a complete count. Actual signal coverage also varies substantially by terrain, atmospheric conditions, and network load, and the 5 km service radius threshold applied in the ANI is a simplified approximation of real world coverage polygons that would require RF propagation modelling to compute precisely.

**ZHI weight calibration.** The sub index weights (0.35 PHI, 0.35 ANI, 0.30 SRI) were established through structured expert elicitation during project design and have not been empirically validated against household survey data, observed disaster outcomes, or mortality records. It is possible that weight adjustments based on empirical calibration would alter settlement rankings, particularly in cases where ZHI scores are clustered near class boundaries. Future work should incorporate community level survey data and, where available, historical disaster impact records to validate and refine the weighting scheme.

**DEM resolution and flat delta precision.** The SRTM 30 m DEM, acquired in 2000 and used as the primary elevation input to the PHI, has a vertical accuracy of approximately ±5–10 m RMSE in low relief terrain, which is insufficient for the ±1 m precision required to accurately characterise flood exposure across the Jamuna Padma delta. Much of the study area lies within a 1–5 m elevation band, meaning that small DEM errors can produce large classification errors in hazard zonation. Higher resolution topographic data (TanDEM X, AW3D30, or local LiDAR surveys) would substantially improve the physical hazard component of the analysis.

**Char boundary timing and seasonality.** The char boundaries and erosion accretion measurements in this study are derived from Sentinel 2 dry season composites assembled to minimise cloud cover and standing water, following the standard practice in char mapping literature. The resulting boundaries represent low water season extents and differ substantially from monsoon peak extents, which may be 30–60% smaller in area for mid channel chars. Settlement populations and infrastructure inventories are similarly season dependent. The ZHI analysis therefore characterises flood season exposure from a conservative baseline, and peak season analyses using monsoon phase imagery would likely yield higher hazard scores for mid channel settlements.

   

## Policy Recommendations

**1. Elevated community FM broadcast towers in Critical Zero Hour chars.** The analysis identifies 11 settlements in the Critical Zero Hour class, all with ZHI scores above 75 and an average of 347 days of road isolation per year. For these communities, mobile network connectivity is structurally unreliable during flood events regardless of tower density, because the towers themselves are at flood risk. A resilient alternative is dedicated elevated FM radio broadcast infrastructure towers mounted above 8 m above high water datum (AHD) connected to district level meteorological alert systems. FM radio receivers are low cost, require no data connectivity, and function during power outages with battery backup. Such infrastructure should be integrated into the existing BDRCS and BMD early warning dissemination framework.

**2. SMS independent last mile warning protocols.** Policy frameworks for last mile EWS delivery in the Jamuna Padma corridor should explicitly specify redundant protocols that do not depend on SMS or data network availability. Siren relay systems (sequential siren activation across char settlement clusters), boat runner networks (designated community volunteers with predefined alert routes), and megaphone equipped community response teams represent proven, low technology alternatives that have been deployed effectively in comparable delta environments in Vietnam and Myanmar. Formalising these protocols within union level disaster management plans and funding their annual training and equipment maintenance would substantially reduce Zero Hour exposure at zero dependence on telecommunications infrastructure.

**3. HAPS pilot for monsoon season continuous coverage.** High Altitude Platform Stations (HAPS) stratospheric aircraft or balloons operating at 15–25 km altitude offer a technologically credible pathway to providing continuous broadband and narrowband telecommunications coverage over flood isolated regions during the monsoon season. Unlike satellite based systems, HAPS can be regionally deployed and repositioned in response to forecast flood events, providing targeted coverage precisely over areas where ground based infrastructure has failed. A RIMES coordinated pilot of HAPS based emergency communications over the Jamuna Padma corridor during one monsoon season would provide critical empirical data on feasibility, latency, and coverage reliability.

**4. Annual GEE automated ZHI recalculation.** The Zero Hour Atlas pipeline is designed for annual update with minimal manual intervention. The GEE scripts can be triggered automatically using the Earth Engine API to ingest the latest Sentinel 1 imagery, recompute flood frequency surfaces, and regenerate ZHI scores for all 59 analysis points. Integrating this recalculation into the RIMES annual geospatial monitoring cycle would enable real time tracking of infrastructure gap dynamics as char morphology evolves, towers are added or removed, and roads are constructed or eroded. This provides a systematic, evidence based foundation for annual infrastructure investment prioritisation and EWS coverage gap closure reporting.

   

## References

1. RIMES (2025). *Impact Based Forecasting for Last Mile Delivery in South Asia: Strategic Framework 2025–2030.* Regional Integrated Multi Hazard Early Warning System, Bangkok.

2. United Nations (2022). *Early Warnings for All (EW4All): Executive Action Plan 2023–2027.* United Nations Office for Disaster Risk Reduction (UNDRR), Geneva.

3. Uddin, K., Matin, M.A., & Meyer, F.J. (2019). Operational flood mapping using multi temporal Sentinel 1 SAR images: A case study from Bangladesh. *Remote Sensing*, 11(13), 1581. https://doi.org/10.3390/rs11131581

4. Islam, A.S., Bala, S.K., & Haque, M.A. (2010). Flood inundation map of Bangladesh using MODIS time series images. *Journal of Flood Risk Management*, 3(3), 210–222. https://doi.org/10.1111/j.1753 318X.2010.01074.x

5. Seijger, C., van Halsema, G., Wiberg, D., & Douven, W. (2019). Understanding policy implementation failure in riverine delta early warning systems. *International Journal of Disaster Risk Reduction*, 34, 107–116. https://doi.org/10.1016/j.ijdrr.2018.11.008

6. OpenCelliD (2025). *Global Mobile Cell Tower Database.* Unwired Labs. https://opencellid.org

7. ESA Copernicus Programme (2024). *Sentinel 1 SAR GRD: C band Synthetic Aperture Radar Ground Range Detected.* European Space Agency. https://developers.google.com/earth engine/datasets/catalog/COPERNICUS_S1_GRD

8. Farr, T.G., et al. (2007). The Shuttle Radar Topography Mission. *Reviews of Geophysics*, 45(2), RG2004. https://doi.org/10.1029/2005RG000183

9. Bangladesh Bureau of Statistics (2022). *Population and Housing Census 2022: Preliminary Report.* BBS, Dhaka.

   

## BibTeX Citation

```bibtex
@misc{zerohour2026,
  title        = {Zero Hour Atlas: Satellite Derived Mapping of Early Warning Last Mile
                  Reach Gaps in the Jamuna Padma Char Corridor, Bangladesh},
  author       = {[Md Sabbir Islam]},
  year         = {2026},
  howpublished = {RIMES Mapathon 2026},
  note         = {Theme 01: Geospatial Data for Disaster Risk Assessment}
}
```

   

## License

This project is released under the [MIT License](LICENSE).

```
MIT License

Copyright (c) 2026 [Md Sabbir Islam]

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
```

   

*Submitted to the RIMES Mapathon 2026   Theme 01: Geospatial Data for Disaster Risk Assessment.*
*All data, code, and outputs are freely available under the terms of the MIT License.*
