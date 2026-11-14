# 11-Sound_30DaysMapChallenge_Hargeisa-Road-Traffic-Noise-Mapping

### A GIS-based preliminary spatial screening model for assessing potential road traffic noise influence in Hargeisa, Somaliland

<img width="3900" height="3000" alt="Hargeisa_Urban_Traffic_Noise_Potential" src="https://github.com/user-attachments/assets/e0e2ae93-00c6-4853-ac10-a6aa6d835c24" />
<img width="4200" height="3300" alt="Hargeisa_Traffic_Noise_Potential" src="https://github.com/user-attachments/assets/f8b638af-2388-47f3-9bb3-cc73241a6751" />




## 1. Project Overview

This project explores the potential spatial influence of road traffic on the urban environment of Hargeisa, Somaliland, using OpenStreetMap road network data and geospatial modelling in R.

The analysis develops a preliminary traffic-noise potential index by combining road classification, analyst-defined road weights, and distance-based decay functions.

The underlying assumption is that roads with higher traffic potential, such as primary and secondary roads, may exert greater noise influence on nearby areas than smaller local roads. This influence is modelled as decreasing with distance from the road network.

The resulting raster surface provides a spatial overview of areas that may experience relatively higher road-related noise influence.

The project also incorporates building footprints and road networks to provide detailed urban context and produces both a district-level map and a zoomed-in urban map.

## 2. Project Objectives

The main objectives are to:

- Extract and prepare the Hargeisa road network using OpenStreetMap data.
- Identify and classify roads according to their functional hierarchy.
- Calculate road lengths and examine the spatial distribution of road infrastructure.
- Develop a relative road traffic noise potential index using road weights and distance decay.
- Generate a 50-metre-resolution raster surface.
- Identify areas with relatively higher potential road noise influence.
- Produce publication-quality maps for spatial interpretation and urban analysis.

## 3. Study Area

The study focuses on Hargeisa, Somaliland.

The district boundary defines the main spatial analysis extent, while a smaller urban extent is generated around the densest concentration of building footprints.

<insert-study-area-map-here>

**Coordinate Reference System:** WGS 84 / UTM Zone 38N (EPSG:32638).

## 4. Data Sources

The project uses the following spatial datasets:

| Dataset | Description | Source |
|---|---|---|
| Road network | Road features and highway classifications | OpenStreetMap |
| Building footprints | Building polygons within the study area | OpenStreetMap |
| Administrative boundary | Hargeisa study area boundary | Project boundary dataset |

### OpenStreetMap attributes

The road extraction process retains additional attributes where available, including:

- `highway`
- `lanes`
- `maxspeed`
- `surface`

These attributes can support more detailed traffic and road-environment analysis in future model development.

**Data attribution:** © OpenStreetMap contributors, available under the Open Database License (ODbL).

## 5. Methodology

The workflow consists of several stages, from data extraction and quality checks to spatial modelling and cartographic visualization.

### 5.1 Data Extraction and Preparation

Road networks and building footprints are extracted from a locally downloaded Somalia OpenStreetMap PBF file.

The `osmextract` package is used to extract the relevant features within the Hargeisa study boundary.

The datasets are then:

- Validated and transformed into EPSG:32638.
- Filtered to retain relevant road and building features.
- Clipped to the Hargeisa administrative boundary.
- Exported as GeoPackage files for subsequent analysis.

### 5.2 Road Network Classification

Roads are classified using their OpenStreetMap `highway` attributes.

The model uses the following categories:

| Road classification | OSM highway types |
|---|---|
| Major roads | trunk, trunk_link, primary, secondary |
| Tertiary roads | tertiary |
| Local roads | residential, living_street, unclassified |
| Service roads | service |
| Tracks | track |

Other road types are excluded from the noise potential modelling stage.

### 5.3 Road Network Assessment

Before modelling, the road network is assessed to understand its completeness and spatial characteristics.

The assessment includes:

- Number of road features.
- Road geometry validity.
- Coordinate reference system verification.
- Total road length in kilometres.
- Road segment counts by highway classification.
- Road length distribution by road type.
- Comparison of road features before and after boundary clipping.

Road length summaries are exported to Excel for further analysis.

### 5.4 Road Weight Assignment

Each road category receives a relative influence weight based on its assumed potential to generate traffic noise.

| Road category | Assigned weight |
|---|---:|
| Major roads | 0.90 |
| Tertiary roads | 0.65 |
| Local roads | 0.40 |
| Service roads | 0.25 |
| Tracks | 0.15 |

These weights are analyst-defined assumptions used for preliminary spatial screening. They are not measured traffic volumes or acoustic emission coefficients.

### 5.5 Distance-Based Noise Influence Modelling

The model generates a spatial influence surface around each road category.

The influence of each road category is calculated using an exponential distance-decay function:

\[
S_i(d)=W_i \times e^{-d/250}
\]

Where:

- \(S_i(d)\) = Relative influence of road category \(i\) at distance \(d\).
- \(W_i\) = Assigned weight for the road category.
- \(d\) = Distance from the nearest road of that category, in metres.
- 250 = Distance-decay parameter in metres.

This function assumes that potential road-related influence decreases exponentially with distance from the road.

The strongest influence from the different road categories is retained at each raster cell:

\[
S(x)=\max_i\left(S_i(d)\right)
\]

The resulting surface is normalized to a relative index ranging from 0 to 100:

\[
NoiseIndex(x)=\frac{S(x)}{0.90}\times100
\]

### 5.6 Raster Generation

The model uses a raster resolution of **50 metres**.

A study-area mask is generated from the Hargeisa boundary, and road influence surfaces are calculated and combined within the study area.

The final raster is exported as a GeoTIFF for use in GIS software such as QGIS and ArcGIS.

### 5.7 Urban-Level Mapping

A zoomed-in urban map is also generated to provide greater detail.

The workflow:

1. Calculates building centroids.
2. Creates a 500-metre grid.
3. Counts building centroids within each grid cell.
4. Identifies the grid cell with the highest building concentration.
5. Uses this location as the approximate urban centre.
6. Creates a 6.5-kilometre buffer around the centre.
7. Crops roads, buildings, boundaries, and the noise potential raster to the selected extent.
8. Simplifies geometries for faster cartographic rendering.

The resulting map provides a more detailed view of the spatial distribution of potential road noise influence within the urban area.

## 6. Technical Stack

The project was developed using R and open-source geospatial packages.

| Package | Main application |
|---|---|
| `sf` | Vector data processing and spatial operations |
| `terra` | Raster processing, distance calculations, and rasterization |
| `osmdata` | OpenStreetMap query functionality |
| `osmextract` | Local OSM PBF extraction |
| `dplyr` | Data manipulation and summarization |
| `ggplot2` | Spatial visualization |
| `ggspatial` | Map elements, north arrows, and scale bars |
| `viridis` | Colour palette utilities |
| `scales` | Visualization scaling |
| `patchwork` | Combining plots |
| `units` | Unit handling |
| `openxlsx` | Excel output |
| `ragg` | High-quality image rendering |

## 7. Project Structure

The suggested repository structure is:

```text
Hargeisa-Road-Traffic-Noise/
│
├── Data/
│   ├── Boundaries/
│   ├── Roads/
│   ├── Buildings/
│   └── OSM/
│
├── Outputs/
│   ├── Raster/
│   ├── Maps/
│   └── Tables/
│
├── R/
│   └── Hargeisa_Road_Traffic_Noise.R
│
└── README.md
```

## 8. Main Outputs

The project generates the following outputs:

| Output | Format | Description |
|---|---|---|
| Hargeisa road network | GeoPackage | Extracted OSM road features |
| Clipped road network | GeoPackage | Roads clipped to the study boundary |
| Building footprints | GeoPackage | Building polygons |
| Road summary | Excel | Road segment counts and lengths by classification |
| Traffic noise potential | GeoTIFF | 50-metre relative influence raster |
| District-level map | PNG | Traffic noise potential across the study area |
| Urban-level map | PNG | Detailed visualization of the urban area |


## 9. How to Run the Project

### Prerequisites

- R (recommended: a recent version).
- RStudio (optional).
- A Somalia OpenStreetMap PBF extract.
- Hargeisa administrative boundary dataset.

### Install packages

```r
packages <- c(
  "sf",
  "terra",
  "osmdata",
  "osmextract",
  "dplyr",
  "ggplot2",
  "ggspatial",
  "viridis",
  "scales",
  "patchwork",
  "units",
  "openxlsx",
  "ragg"
)

install.packages(packages)
```

### Prepare input data

Place the Somalia OSM PBF extract in:

```text
Data/OSM/somalia-261112.osm.pbf
```

Ensure that the Hargeisa boundary is available at:

```text
Data/Boundaries/Hargeisa_District.gpkg
```

Update the project working directory and file paths in the R script to match your local environment.

### Execute the workflow

Open the main R script in RStudio and run the sections sequentially.

The workflow extracts the OSM data, prepares the road and building datasets, calculates road influence, generates raster outputs, and exports the final maps.

## 10. Model Limitations and Interpretation

**This is a preliminary spatial screening model, not a calibrated environmental noise assessment.**

The relative index should not be interpreted as actual sound pressure levels, noise exposure, or decibels (dB or dB(A)).

The model has several important limitations:

- Road classification is used as a proxy for potential traffic activity.
- Assigned road weights are analyst-defined rather than derived from observed traffic volumes.
- The model does not incorporate actual vehicle counts, vehicle composition, or traffic speed.
- The model does not account for road width, pavement characteristics, or temporal traffic patterns.
- Building height, density, façade materials, and sound insulation are not incorporated into acoustic propagation.
- Terrain, barriers, vegetation, and atmospheric conditions are not explicitly modelled.
- The exponential distance-decay function is a simplified representation of spatial influence.
- OpenStreetMap coverage and classification quality may vary across the study area.

The output is therefore best used to identify locations for further investigation, rather than to establish regulatory noise levels or population exposure.

## 11. Potential Future Improvements

Future development could strengthen the model by incorporating:

- Traffic volume and vehicle count observations.
- Vehicle type and composition.
- Observed or estimated traffic speeds.
- Road width and surface characteristics.
- Building height and urban morphology.
- Population distribution and sensitive receptors, such as schools and hospitals.
- Terrain and physical noise barriers.
- Field-based sound measurements for calibration and validation.
- Established environmental noise modelling approaches, such as CNOSSOS-EU or other suitable acoustic models.

These improvements would help move the project from preliminary spatial screening towards a more comprehensive urban environmental noise assessment.

## 12. Acknowledgements

This project uses open-source geospatial technologies and OpenStreetMap data.

- [OpenStreetMap](https://www.openstreetmap.org/)
- [R Project](https://www.r-project.org/)
- [sf package](https://r-spatial.github.io/sf/)
- [terra package](https://rspatial.org/terra/)
- [osmextract package](https://docs.ropensci.org/osmextract/)
- [ggplot2](https://ggplot2.tidyverse.org/)

**Data attribution:** © OpenStreetMap contributors. Data available under the [Open Database License (ODbL)](https://www.openstreetmap.org/copyright).

## 13. License

Specify a license for the original project code before publication. The OpenStreetMap data remain subject to their own licensing terms (ODbL).

---

**Project:** Hargeisa Road Traffic Noise Mapping  
**Study Area:** Hargeisa, Somaliland  
**Method:** Road classification and exponential distance-decay modelling  
**Raster Resolution:** 50 metres  
**Coordinate Reference System:** EPSG:32638  
**Tools:** R | GIS | OpenStreetMap | Spatial Analysis
