# Spatial Analysis of NASA FIRMS VIIRS Fire Hotspots in Indonesia (Aug–Sep 2026)

Spatial analysis of NASA FIRMS VIIRS active-fire detections across Indonesia, with province-level density mapping and an interactive 3D visualization.

## Overview

This project identifies where fire hotspots concentrate in Indonesia during the peak dry season. It uses NASA FIRMS VIIRS detections from **1 August to 30 September 2026** and GADM province boundaries. The workflow cleans the raw data, assigns every hotspot to a province with a spatial join, and normalizes the counts by province area so that large and small provinces can be compared fairly.

## Key Findings

- **Central Kalimantan** has the highest hotspot density: about **654 hotspots per 1,000 km²** (100,452 detections in total).
- **South Kalimantan** (≈ 439) and **West Kalimantan** (≈ 426) follow, then **South Sumatra** (≈ 320). Kalimantan dominates the top ranks.
- **Papua** has a large raw count (≈ 40,000) but drops out of the top 10 once normalized by area, so raw counts alone are misleading.

## Features

- Data cleaning: duplicate removal and filtering of low-confidence detections
- Conversion of latitude/longitude records into a GeoDataFrame (EPSG:4326)
- Spatial join between hotspot points and province polygons
- Hotspot counts, percentage shares, and a top-10 bar chart per province
- Choropleth maps of raw counts and of density per 1,000 km² (area computed in the equal-area CRS EPSG:6933)
- Daily hotspot trend and weekly trend for the top provinces
- Fire Radiative Power (FRP) statistics per province (count, mean, median, max)
- DBSCAN clustering of hotspots in Kalimantan (haversine metric, 5 km neighborhood)
- Interactive 3D hexagon map exported to `hotspots_3d.html` (PyDeck)

## Data Sources

| Data | Source |
|---|---|
| Active-fire detections (VIIRS) | [NASA FIRMS](https://firms.modaps.eosdis.nasa.gov) |
| Province boundaries (level 1) | [GADM v4.1](https://gadm.org) |

The expected input files are:

- `fire_nrt_SV-C2_818013.csv` (FIRMS VIIRS download for Indonesia)
- `gadm41_IDN_1.shp` together with its `.shx`, `.dbf`, and `.prj` files

## Tech Stack

Python · pandas · GeoPandas · Matplotlib · NumPy · scikit-learn · Folium · mapclassify · PyDeck

## Getting Started

### Prerequisites

- Python 3.10 or newer
- Jupyter Notebook or JupyterLab

### Installation

```bash
git clone <your-repository-url>
cd <your-repository-folder>
pip install pandas geopandas matplotlib numpy scikit-learn folium mapclassify pydeck
```

### Run

1. Download the FIRMS CSV and the GADM shapefile, and place them in one folder.
2. Open the notebook and update the file paths in the first cells, for example:

   ```python
   df = pd.read_csv(r"C:\path\to\fire_nrt_SV-C2_818013.csv")
   prov = gpd.read_file(r"C:\path\to\gadm41_IDN_1.shp")
   ```

3. Run all cells from top to bottom.
4. Open the generated `hotspots_3d.html` in a browser and drag to rotate the 3D map.

## Methodology

1. **Clean:** drop duplicates and remove detections with `confidence == "l"`.
2. **Georeference:** build a GeoDataFrame from longitude/latitude with CRS EPSG:4326.
3. **Join:** `gpd.sjoin(..., predicate="within")` assigns each hotspot to a province.
4. **Normalize:** project provinces to EPSG:6933, compute area in km², and calculate hotspots per 1,000 km².
5. **Analyze:** trends over time, FRP statistics, and DBSCAN clusters.
6. **Visualize:** bar charts, choropleth maps, and a 3D hexagon map.

## Project Structure

```
.
├── notebook.ipynb      # main analysis notebook
├── hotspots_3d.html    # generated 3D map
└── README.md
```

## Limitations

- The data covers only two months of the peak dry season, so results should not be generalized to the full year.
- Hotspots are satellite detections, not burned area. One large fire can produce many points.
- Some detections (for example, in eastern Indonesia) may be savanna or agricultural burning rather than forest fires.
- Removing low-confidence points reduces false alarms but may also drop some real small fires.
- GADM level-1 boundaries are coarse; finer units would give more precise results.

## Acknowledgements

- NASA FIRMS for the active-fire data
- GADM for the administrative boundaries

## License

Add your preferred license here (for example, MIT). Note that FIRMS and GADM data have their own terms of use.
