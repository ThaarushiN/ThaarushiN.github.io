---
title: "River Meander Migration Prediction using Temporal Convolutional Networks"
category: "Academic"
date: 2024-03-01
excerpt: "Applied deep learning and remote sensing data to predict future river path changes in the Deduru Oya basin."
header:
  overlay_image: /assets/images/meander-prediction.png
  overlay_filter: 0.3
tags: [Deep Learning, TCN, GIS, Remote Sensing, Explainable AI]
layout: single
author_profile: true
read_time: true
share: true
related: true
---

This project explored how deep learning and satellite imagery can be used to **predict river meander migration**, especially in fast-changing alluvial systems. The work centered on the **Lower Deduru Oya** in Sri Lanka, an area where rapid channel shifts continue to threaten nearby infrastructure.

Traditional hydrological models depend heavily on parameters that are often expensive or impractical to obtain. This study addressed that gap by building a predictive workflow that works entirely with freely available remote-sensing data.



### Technical Approach

A full pipeline was developed to move from raw imagery to a deployable predictive model:

#### **Data Acquisition & Processing**

* Built a long-term **time-series dataset** using nearly 40 years of LANDSAT 5 and LANDSAT 8 imagery.
* Implemented an automated processing workflow using **OpenCV**, **Pillow**, **Rasterio**, and **GDAL** to extract river centerlines and compute meander migration (centerline shifts).

#### **Data Preparation**

* Cleaned and inspected the resulting dataset manually where needed.
* Filled missing values using **PCHIP interpolation**.
* Applied a median filter to reduce noise and smooth abrupt inconsistencies in the time series.

#### **Model Development**

Two modeling strategies were evaluated:

1. **Temporal Convolutional Network (TCN)**
   Designed to capture complex temporal patterns using only historical satellite-derived migration data.

2. **Multi-Output Regressor XGBoost**
   Used to explicitly relate migration behavior to external hydrometeorological variables such as precipitation, runoff, and vegetation indices.

#### **Explainability**

* Applied **Saliency Mapping** to interpret the TCN’s temporal dependencies.
* Used **feature-importance plots** to understand how external variables influenced XGBoost predictions.



### Key Findings

* **TCN Model:** Achieved an **R² of 0.71** with an MSE of 0.30, showing that meaningful predictions can be made using satellite history alone.
* **XGBoost Model:** Performed slightly better when external drivers were included, with **R² values between 0.692–0.782** across multiple control points.
* **Overall Insight:** Deep learning—particularly TCNs—proved more capable than conventional regression approaches for capturing the non-linear and seasonal behavior of the Lower Deduru Oya’s meander migration.



### Application: *RiverInsight*

The final models were integrated into **RiverInsight**, an expert tool built to assist civil engineers and planners.

* **Stack:** Flask backend + React.js frontend
* **Design:** Layered architecture focused on fast inference and intuitive visualization
* **Purpose:** Provide actionable, explainable predictions for river-related infrastructure decisions.

---

### Data Sources

All spatial and temporal data were processed through **Google Earth Engine (GEE)**.

**Satellite Imagery (Centerline Extraction):**

* **LANDSAT 8 (OLI/TIRS):** `LANDSAT/LC08/C02/T1_L2`
* **LANDSAT 5 (TM):** `LANDSAT/LT05/C02/T1_L2`
  Data courtesy of **USGS** and **NASA**.

**Hydrological & Meteorological Variables (XGBoost Model):**

* **ERA5 Reanalysis Dataset** (ECMWF), accessed via GEE and the Copernicus Data Store.

