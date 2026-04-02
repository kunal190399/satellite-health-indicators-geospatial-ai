# Satellite-Derived Urban Health Indicators using Geospatial AI

A geospatial AI pipeline for transforming multi-sensor satellite imagery into interpretable, uncertainty-aware environmental health indicators for urban areas — built as research preparation for the Space2Health PhD project at the University of Liverpool.

---

## Overview

High-resolution satellite data from missions like Sentinel-1, Sentinel-2, and Landsat-8/9 now provides continuous, city-wide measurements of environmental conditions linked to public health: vegetation cover, land surface temperature, air quality, impervious surfaces, and green space access. Yet a critical methodological gap remains — many existing health indicators rely on simplified legacy metrics that were not designed with health inference as their primary objective.

This project addresses that gap by building a complete pipeline from raw satellite-derived features to interpretable, policy-relevant health indicators, with explicit attention to:

- **Methodological rigour** — how modelling choices shape what environmental signals are captured
- **Interpretability** — which satellite features most strongly drive health risk predictions
- **Uncertainty quantification** — bootstrap confidence intervals for every spatial prediction
- **Spatial equity** — whether environmental health burdens are distributed fairly across urban zone types

---

## Motivation

This work is directly informed by the Space2Health research agenda (Dr Ron Mahabir and Dr D Arribas-Bel, University of Liverpool), which investigates how satellite imagery can be transformed into meaningful measures of environmental exposures linked to health. The methodological questions at the heart of Space2Health — how do design decisions about spatial resolution, temporal aggregation, feature engineering, and model architecture shape health-relevant inference? — are the same questions this pipeline is built to explore.

---

## Pipeline

```
Multi-sensor satellite feature extraction
(Sentinel-1 SAR, Sentinel-2 optical, Landsat-8/9 thermal)
        ↓
Feature engineering — 6 satellite-derived indicators per zone
(NDVI, LST, impervious fraction, PM2.5 proxy, green access, building density)
        ↓
StandardScaler normalisation
        ↓
UMAP dimensionality reduction (6D → 2D spatial embeddings)
        ↓
KMeans clustering — urban environmental typology
(silhouette-based optimal k selection)
        ↓
Environmental Health Risk Index (EHRI) construction
(domain-knowledge weighted composite indicator)
        ↓
Gradient Boosting surrogate model (learns EHRI from raw features)
        ↓
Bootstrap uncertainty quantification (50 resampled models)
        ↓
Permutation importance — explainability layer
        ↓
Spatial equity analysis — EHRI distribution by urban zone type
```

---

## Features Extracted

| Feature | Satellite source | Health relevance |
|---|---|---|
| NDVI (Normalised Difference Vegetation Index) | Sentinel-2 optical | Mental health, heat island mitigation |
| Land Surface Temperature (LST) | Landsat-8/9 thermal | Heat stress, cardiovascular risk |
| Impervious surface fraction | Sentinel-1 SAR | Flood risk, urban heat, runoff |
| PM2.5 air quality proxy | Sentinel-5P / derived | Respiratory disease, cardiovascular |
| Green space access score | Derived from optical | Physical activity, mental health |
| Building density | Sentinel-1 SAR backscatter | Overcrowding, air quality, heat |

---

## Key Results

- Urban environmental typology identified via UMAP + KMeans clustering reveals distinct zone types — dense urban core, residential suburbs, green periphery — with significantly different health risk profiles
- Gradient Boosting surrogate model achieves **R2 > 0.95** in predicting the composite EHRI from raw satellite features
- Bootstrap uncertainty quantification provides confidence intervals for every spatial prediction, enabling honest communication of model limitations
- Feature importance analysis identifies **Land Surface Temperature** and **impervious surface fraction** as the strongest drivers of environmental health risk — consistent with urban heat island and flooding literature
- Spatial equity analysis reveals an **inequality gap** of approximately 0.4 EHRI units between highest and lowest risk urban zone types, with risk concentrated in the dense urban core

---

## Outputs

| File | Description |
|---|---|
| `satellite_clustering.png` | Three-panel figure: spatial cluster map, UMAP embedding, LST urban heat island |
| `health_indicator_explainability.png` | Three-panel figure: feature importance, EHRI spatial map, equity analysis |

---

## Technical Approach

### Multi-sensor feature engineering
The pipeline simulates the multi-sensor data fusion approach central to modern Earth Observation health research. Optical bands (Sentinel-2) provide vegetation and land use information. Thermal imagery (Landsat-8/9) captures surface temperature. SAR backscatter (Sentinel-1) enables impervious surface and building density estimation regardless of cloud cover. Combining these modalities captures complementary aspects of the urban environment that no single sensor can provide alone.

### UMAP spatial embeddings
UMAP (Uniform Manifold Approximation and Projection) reduces the 6-dimensional satellite feature space to 2 dimensions while preserving local neighbourhood structure. This produces a map of urban environmental similarity — zones that cluster together in UMAP space share similar satellite signatures and, by extension, similar health exposure profiles. This approach connects to the spatial embedding representations discussed in recent geospatial foundation model literature.

### Environmental Health Risk Index (EHRI)
The EHRI is a composite indicator constructed using domain knowledge weights derived from the environmental health literature. Unlike purely data-driven approaches, the EHRI embeds scientific knowledge about which environmental conditions matter most for health outcomes. The weighting scheme is transparent and adjustable, enabling researchers to test sensitivity to methodological choices — a key focus of the Space2Health research agenda.

### Uncertainty quantification
Bootstrap resampling across 50 model instances provides an empirical estimate of prediction uncertainty for each spatial zone. Zones with high uncertainty — typically at the boundary between environmental types — are flagged for additional investigation. Honest uncertainty communication is essential for policy-relevant health indicators: decision-makers need to know not just what the model predicts but how confident the prediction is.

### Spatial equity analysis
Health burdens are not distributed randomly across cities. The equity analysis compares EHRI distributions across urban zone types, revealing which communities face disproportionate environmental health risks. This analysis connects satellite-derived indicators to questions of social justice and equitable resource allocation — the policy dimension at the heart of the Space2Health project.

---

## Requirements

```bash
pip install rasterio geopandas scikit-learn matplotlib seaborn numpy pandas umap-learn
```

| Library | Purpose |
|---|---|
| rasterio | Raster satellite data I/O |
| geopandas | Geospatial vector data handling |
| umap-learn | Dimensionality reduction and spatial embedding |
| scikit-learn | ML modelling, clustering, explainability |
| matplotlib / seaborn | Scientific visualisation |
| numpy / pandas | Data handling and computation |

---

## Usage

Run the notebook cell by cell:

```bash
jupyter notebook satellite_health_indicators.ipynb
```

All output figures are saved automatically to the working directory.

---

## Connection to Space2Health PhD Research

This project is built as preparation for the Space2Health PhD studentship (reference SOES003) at the University of Liverpool, supervised by Dr Ron Mahabir and Dr D Arribas-Bel.

**Methodological gap addressed:** The pipeline demonstrates how modelling choices — feature selection, spatial resolution, aggregation strategy, model architecture — shape the health-relevant signals extracted from satellite data. This is precisely the methodological innovation the PhD targets.

**Next steps toward the PhD:**

- Replace simulated data with real Sentinel-1/2 and Landsat-8/9 data accessed via Google Earth Engine
- Implement convolutional neural network and transformer-based architectures for spatial feature extraction
- Integrate graph neural networks to model spatial neighbourhood dependencies between urban zones
- Validate EHRI against real health outcome data from NHS and ONS sources
- Extend equity analysis to include socioeconomic deprivation indices (e.g. IMD) for intersectional vulnerability mapping
- Apply the framework to empirical case studies in Liverpool and other UK cities

---

## References

Small, C., & Elvidge, C. D. (2013). Night on Earth: Mapping decadal changes of anthropogenic night light in Asia. *International Journal of Applied Earth Observation and Geoinformation*, 22, 40-52.

Muller, C. L., Chapman, L., Johnston, S., et al. (2015). Crowdsourcing for climate and atmospheric sciences. *International Journal of Climatology*, 35(11), 3185-3201.

Janowicz, K., et al. (2020). GeoAI: Spatially explicit artificial intelligence techniques for geographic knowledge discovery and beyond. *International Journal of Geographical Information Science*, 34(4), 625-636.

Kuffer, M., Pfeffer, K., & Sliuzas, R. (2016). Slums from space — 15 years of slum mapping using remote sensing. *Remote Sensing*, 8(6), 455.

Mahabir, R., et al. (2018). Critical review of high and very high-resolution remote sensing approaches for detecting and mapping slums. *International Journal of Applied Earth Observation and Geoinformation*, 70, 9-21.

---

## Author

**Kunal Kamble**
MSc Advanced Computer Science, University of Liverpool
[LinkedIn](https://linkedin.com/in/kunal-kamble19) | [Email](mailto:kamblekunal165@gmail.com) | [GitHub](https://github.com/kunal190399)

*Built in preparation for the Space2Health PhD studentship (SOES003) at the University of Liverpool, supervised by Dr Ron Mahabir and Dr D Arribas-Bel.*
