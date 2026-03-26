# ZHI Framework — Technical Note

## Zero Hour Index (ZHI) Methodology

**Project:** Zero Hour Atlas  
**Competition:** RIMES Mapathon 2026 — Theme 01: Geospatial Data for Disaster Risk Assessment  
**Study Area:** Kazipur (Sirajganj) to Doulatdiya (Rajbari), Jamuna-Padma Char Corridor, Bangladesh  
**Area:** 1,225.2 km² | Bbox: lon 89.61–89.85, lat 23.83–24.82 | Projection: EPSG:3857

---

## 1. Overview

The Zero Hour Index (ZHI) is a composite geospatial indicator quantifying Early Warning System (EWS) last-mile delivery failure risk. It fuses three independent sub-indices:

```
ZHI = 0.35 × PHI + 0.35 × ANI + 0.30 × SRI
```

All sub-indices are normalised to 0–1 before compositing. Final ZHI is scaled 0–100.

---

## 2. Sub-Index Definitions

### 2.1 Physical Hazard Index (PHI) — Weight: 35%

**Purpose:** Quantifies flood exposure intensity at each analysis point.

**Formula:**  
`PHI = flood_frequency × elevation_risk_weight`

**Data inputs:**
- Sentinel-1 GRD SAR (~160+ images, 2017–2024) via Google Earth Engine
- SRTM 30m DEM (NASA via GEE)

**Computation:**
- Flood frequency = pixel-wise proportion of S1 SAR scenes classified as water (backscatter threshold < −1 dB)
- Elevation risk = inverse SRTM DEM normalised to 0–1 (lower elevation → higher risk)
- PHI = product of flood frequency and elevation risk, normalised

**Thresholds:**
- PHI ≥ 0.85: Extreme Hazard
- PHI 0.65–0.85: Very High
- PHI 0.45–0.65: High
- PHI 0.25–0.45: Moderate
- PHI < 0.25: Low

**Study findings:**  
Mean PHI = 0.392. Maximum = 0.912. All char settlements PHI = 1.0 (maximum physical hazard class).

---

### 2.2 Access Network Gap Index (ANI) — Weight: 35%

**Purpose:** Measures physical isolation from safety infrastructure during flood events.

**Formula:**  
`ANI = normalised(road_density_gap + shelter_distance)`

**Data inputs:**
- OpenStreetMap road network (2024)
- OSM cyclone shelter registry (2024)

**Computation:**
- Road density gap = inverse of OSM road network kernel density (km/km²), normalised 0–1
- Shelter distance = Euclidean distance to nearest registered flood shelter (km), normalised
- ANI = mean of normalised road gap and shelter distance

**Thresholds:**
- ANI ≥ 0.85: Extreme Access Gap
- ANI 0.65–0.85: Severe
- ANI 0.45–0.65: Moderate
- ANI < 0.45: Low

**Study findings:**  
Char settlements average ANI ≈ 0.88, reflecting near-total road and shelter isolation during monsoon peak.

---

### 2.3 Signal Reach Index (SRI) — Weight: 30%

**Purpose:** Quantifies probability of telecommunications communication failure during flood events.

**Formula:**  
`SRI = normalised(telecom_coverage_gap + tower_flood_risk)`

**Data inputs:**
- OpenCelliD mobile tower database — Bangladesh extract (2025)
- Sentinel-1 flood frequency surface (PHI layer)

**Computation:**
- Coverage gap = inverse of OpenCelliD signal coverage density within 5 km radius, normalised
- Tower flood risk = PHI value at tower location (towers in high-flood zones contribute higher SRI)
- SRI = mean of normalised coverage gap and tower flood risk

**Tower inventory:**
- Total towers: 25 (2G, 3G, 4G)
- Flood-risk towers (PHI > 0.5): 10 (40%)
- Coverage radii: 2G ~3 km, 3G ~2 km, 4G ~1.5 km
- Dead zones (>5 km from any tower): all 25 analysis points

**Study findings:**  
68% of study area estimated under signal blackout during peak flood event.

---

## 3. ZHI Classification (5-Class System)

| Class | ZHI Range | Interpretation |
|-------|-----------|----------------|
| EWS Connected | < 15 | Adequate EWS infrastructure; warnings deliverable |
| Partial EWS | 15–35 | Minor gaps; partial coverage; warnings reach most |
| Moderate Gap | 35–55 | Significant EWS coverage deficits |
| Severe Isolation | 55–75 | Major EWS gaps; high failure risk during flood peak |
| Critical Zero Hour | ≥ 75 | Near-total EWS disconnection during flood events |

---

## 4. Weighting Rationale

| Sub-index | Weight | Rationale |
|-----------|--------|-----------|
| PHI | 35% | Physical flood exposure is the primary driver of failure — without inundation there is no EWS gap |
| ANI | 35% | Physical access deprivation is equally fundamental — isolated communities cannot reach shelters or receive ground-level warnings |
| SRI | 30% | Telecommunications failure amplifies the gap but is partially redundant with physical isolation |

Weights derived from expert elicitation process aligned with Bangladesh NDRCC EW4All National Roadmap 2025–2030 priority dimensions.

---

## 5. Data Lineage

```
Sentinel-1 GRD SAR (GEE)
    → pixel-wise water detection
    → flood frequency surface (10m raster)
    → PHI computation
    → tower flood risk (SRI input)

Sentinel-2 NDWI (GEE)
    → annual wet-season composites
    → water/land transition mapping
    → char erosion/accretion time series

SRTM 30m DEM
    → elevation inversion
    → elevation risk weight (PHI input)

OSM Roads + Shelters
    → road kernel density
    → shelter distance raster
    → ANI computation

OpenCelliD Towers
    → coverage radius buffers
    → dead zone identification
    → tower flood risk scoring
    → SRI computation

ZHI = 0.35×PHI + 0.35×ANI + 0.30×SRI
    → 5-class classification
    → spatial maps (MAP01, MAP02)
    → settlement-level scoring (settlements_scored.csv)
```

---

## 6. Limitations

1. **OpenCelliD completeness:** Crowdsourced dataset may undercount recently deployed towers. Actual signal coverage may differ by terrain and atmospheric conditions.
2. **SRTM vertical precision:** 30m DEM provides ±5–10m vertical accuracy on flat delta terrain, insufficient for precise 0–5m elevation risk zonation.
3. **ZHI weight validation:** Weights are expert-elicited. Empirical validation against household-level survey data on actual warning receipt has not been conducted.
4. **Char boundary dynamics:** Settlement locations and char extents defined at peak monsoon timing. Dry-season extents differ substantially.
5. **Temporal gap:** Sentinel-1 archive covers 2017–2024; post-2024 morphodynamic changes not captured.

---

## 7. Reproduction

All analysis was conducted in Google Earth Engine (GEE) with open-access datasets. To reproduce:

```python
# Step 1: Authenticate GEE
import ee
ee.Authenticate()
ee.Initialize()

# Step 2: Load Sentinel-1 collection
s1 = ee.ImageCollection('COPERNICUS/S1_GRD') \
    .filterBounds(study_area) \
    .filterDate('2017-01-01', '2024-12-31') \
    .filter(ee.Filter.eq('instrumentMode', 'IW'))

# Step 3: Compute flood frequency
def classify_water(img):
    return img.select('VV').lt(-1).rename('water')

flood_freq = s1.map(classify_water).mean()

# Step 4: Export to Drive
task = ee.batch.Export.image.toDrive(
    image=flood_freq,
    description='flood_frequency_2017_2024',
    region=study_area,
    scale=10,
    crs='EPSG:3857'
)
task.start()
```

Full notebook: `Zero_Hour_Atlas_Analysis.ipynb`

---

## 8. Citation

```bibtex
@misc{zerohour2026,
  title     = {Zero Hour Atlas: Satellite-Derived Mapping of Early Warning Last-Mile Reach Gaps 
               in the Jamuna-Padma Char Corridor, Bangladesh},
  author    = {[Author Name]},
  year      = {2026},
  note      = {RIMES Mapathon 2026 — Theme 01: Geospatial Data for Disaster Risk Assessment},
  url       = {https://github.com/[username]/zero-hour-atlas}
}
```
