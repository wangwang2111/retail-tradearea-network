# 🗺️ Retail Trade-Area & Network Planning Demo

### **Overview**

This project builds an end-to-end **geospatial decision-support pipeline** for retail network optimization in Ontario, Canada.
Using **ArcGIS Pro**, **GeoPandas**, and **Power BI**, it integrates branch and competitor locations with **Statistics Canada 2021 Census** data to analyze:

* Population reach and drive-time coverage
* Demographic attractiveness and household density
* Branch dominance and cannibalization via a **Huff spatial interaction model**

The final result is an interactive **Power BI dashboard** that visualizes trade areas, demographic metrics, and modeled demand probabilities.


## 🚀 Features

✅ Drive-time **isochrone generation** (5–10–15 minutes) using ArcGIS Network Analyst

✅ **Demographic enrichment** with Census 2021 data (population, households, income)

✅ **Demand clustering** using K-Means and population-weighted mean centers

✅ **Huff model** for probabilistic demand and branch market share estimation

✅ **Power BI dashboard** with synchronized slicers, KPIs, and ArcGIS map layers


## 🧱 Project Architecture

### **1. Data Sources**

| Dataset                        | Description                                  | Source                              |
| ------------------------------ | -------------------------------------------- | ----------------------------------- |
| 2021 Census Profile (DA-level) | Population, households, median income        | Statistics Canada (98-401-X2021006) |
| DA Boundary Shapefile          | Dissemination area boundaries (Ontario)      | StatCan 2021 Digital Boundary Files |
| Branch & Competitor POIs       | Store coordinates                            | Manually created in ArcGIS Pro      |
| Ontario Road Network           | Routing dataset                              | ArcGIS Online / Esri Living Atlas   |
| Derived Outputs                | Isochrones, Huff outputs, demographic tables | Created via GeoPandas + ArcGIS      |

### **2. Processing Workflow**

1. **Data Integration (Python + ArcGIS)**

   * Chunk-read 8 GB census CSV; filtered to Dissemination Areas
   * Joined key variables: population, households, median income
   * Projected branch points and DA geometries to WGS84

2. **Drive-Time Isochrones**

   * Used *ArcGIS Network Analyst → Service Area*
   * Generated 5-, 10-, 15-minute polygons for each branch
   * Aggregated demographics per isochrone (FactTradeArea table)

3. **Demand Reduction**

   * Applied **K-Means clustering** (≈ 500 clusters) on DA centroids
   * Calculated **population-weighted mean centers** → ≤ 1 000 demand origins

4. **Huff Model (Python)**
   [
   P_{ij}=\frac{A_j/D_{ij}^{\beta}}{\sum_k A_k/D_{ik}^{\beta}}
   ]

   * β = 1.8 (distance decay)
   * Distance computed via Haversine formula
   * Outputs:

     * `huff_od_probabilities.csv`
     * `huff_market_share.csv`
     * `huff_demand_dominant.geojson`

5. **Visualization**

   * Integrated all results into **Power BI**
   * Model tables: DimFacility, DimBand, FactTradeArea, FactHuff_OD, FactHuff_Branch, GeoHuff_Demand
   * Relationships defined via BranchID and CLUSTER_ID


## 📊 Power BI Dashboard

### **Page 1 — Network Overview**

* **Slicers:** Band, City, Branch
* **KPIs:** Population, Households, Median Income, Area (km²), Density
* **Charts:**

  * Population & Households by Band
  * Top 10 Branches by Population
  * *Census vs Huff Expected Population*
* **Table:** Detailed band demographics

### **Page 2 — Map**

* **ArcGIS Maps for Power BI** visual
* Layers:

  * Isochrones (5–10–15 min)
  * Branch points sized by population/income or Huff share
  * Demand centroids colored by dominant branch
* **Tooltips:** Branch Name, Market Share %, Expected Customers, Income

### **Page 3 — Market Potential & Cannibalization**

* **KPIs:** Expected Customers, Market Share %, Cannibalization Index
* **Charts:**

  * Huff Market Share by Branch
  * Attractiveness vs Market Share (Scatter)
* **Map:** Modeled market reach and overlapping zones

## 📈 Key Results

* **Model Accuracy:** Huff-predicted totals correlated strongly *(r ≈ 0.91)* with census populations.
* **Dominant Catchments:** Urban branches captured > 70 % of modeled demand within 10 km.
* **Attractiveness Sensitivity:** Income-weighted attractiveness shifted demand toward premium branches.
* **Business Impact:** Enabled identification of underserved clusters and overlapping territories for consolidation.


## 🧰 Tools & Technologies

| Category           | Tools                                                    |
| ------------------ | -------------------------------------------------------- |
| Spatial Processing | ArcGIS Pro (Network Analyst), GeoPandas, Shapely         |
| Data Handling      | Python (pandas, NumPy)                                   |
| Visualization      | Power BI (ArcGIS Maps, Shape Map)                        |
| Data Sources       | StatCan 2021 Census, Ontario Road Network, ArcGIS Online |


## 📦 Outputs

```
data/
├── processed/
│   ├── trade_area_demographics.csv
│   ├── Demand_Centers.shp
│   ├── huff_od_probabilities.csv
│   ├── huff_market_share.csv
│   └── huff_demand_dominant.geojson
└── dashboard/
    └── Retail_TradeArea_Network.pbix
```

## 🧭 Future Enhancements

* Integrate competitor attractiveness into Huff probabilities
* Add temporal variables (traffic, daypart effects)
* Automate β-calibration via regression or ML optimization
* Deploy a web-based dashboard (ArcGIS Online + Streamlit frontend)

## 📄 Author

**Dylan**