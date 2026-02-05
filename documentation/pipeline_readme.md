# Documentation
Open Building Insights is composed of two distinct functional blocks. The first consists of the Python scripts used to extract various [attributes](/documentation/list_attributes/README.md). The second is the [website](https://obi.sdg7energyplanning.org/about.html), which hosts processed information for India and Kenya that users can easily download. This documentation focuses on the data pipeline (see image below) used to enrich building footprints with these attributes.

## Suggested pipeline  
This section outlines the computational workflow used to obtain OBI datasets in India and Kenya. The scripts used are available on [GitHub](https://github.com/orgs/Open-Building-Insights/repositories), where each repository contains the relevant notebooks executed. These include: 
* [Data preprocessing](#data-preprocessing) 	 
* Building classification 
* [Informal settlement detection](#informal-settlement)
* [Socio-economics](#population)
* [Distance Metrics / Proximity Calculations](#proximity-calc)
* [Heat exposure](#heat-exposure)
* [Data Postprocessing](#data-postprocessing)
* [Solar Rooftop Potential](#solar)


![obi-workflow](/documentation/images/workflowv2.png)

<a id="data-preprocessing"></a>

### Data preprocessing

In this first section of the pipeline, the base building footprint dataset is downloaded from VIDA and associated metrics computed. The dataset is further enriched with additional building attributes from public sources.

| Category | Details |
| :--- | :--- |
| **Input** | Boundary shapefile, rasters (Open Buildings 2.5D Temporal, GHSL-SMOD, EarthEnv-DEM90)
| **Requirements** | Runs on local machine|
| **Outputs** | Building footprint dataset with: <br>• Coordinates <br>• ID <br>• Geometry <br>• Area in meters <br>• Perimeter in meters <br>• Number of building faces <br>• Height <br>• Number of floors <br>• Gross floor area (GFA) <br>• Urbanization classes <br>• Elevation

The preprocessing workflow starts by downloading building footprints either as **S2 partitions** for India or as a **single country GeoParquet** for Kenya, then filters footprints to the Area of interest (AOI), applies a minimum confidence threshold of 0.7, removes duplicates, and computes footprint metrics (`area_in_meters`, `perimeter_in_meters`, `building_faces`). Next, the script downloads **Google Open Buildings 2.5D Temporal** rasters for the AOI, samples height pixels within each footprint to compute `height_mean`, `height_median`, and `height_max`, and derives `floors` and `GFA` from the standardized height and footprint area. In the next steps, the script assigns urbanization classes from **GHS-SMOD** and elevation from **EarthEnv-DEM90** to each building. The associated code is available in [Notebooks](https://github.com/Open-Building-Insights/data-preprocessing/tree/main/notebooks) in the [Data Preprocessing repository](https://github.com/Open-Building-Insights/data-preprocessing).



### Building classification 



<a id="informal-settlement"></a>

### Informal settlement detection 

A well-trained machine learning model is used to identify informal settlements in urban areas in a given AOI. 

| Category | Details |
| :--- | :--- |
| **Input** | • Boundary shapefile <br>• GHSL-SMOD raster file <br>• Trained model <br>• Building footprint dataset (with longitude, latitude, area, and height) 
| **Requirements** | Runs on local machine|
| **Outputs** | • Informal classification <br>• Model Confidence 

The script classifies urban areas as formal or informal by replicating the data preparation pipeline used in training, before classification. GHSL-SMOD raster is used as source to extract urban areas within the AOI. The polygons are then divided into uniform square grid tiles with bounding boxes computed to speed up spatial filtering. For each grid, key building statistics (`building count`, `average area`, `maximum area`, and `average height`) are calculated. After applying the same preprocessing steps as training (handling missing values, normalization, dropping non-feature columns), the trained model is applied to generate predictions. The code used is available in the [Informal Settlement Detection](https://github.com/Open-Building-Insights/informal-settlement-detection/tree/main) repository. 


<a id="population"></a>

### Socio-economics 
The population disaggregation workflow relies on the spatial integration of building footprints, administrative boundaries, and demographic data to downscale ward-level projections to the building level. 


| Category | Details |
| :--- | :--- |
| **Input** | • **Building footprints** with: latitude, longitude, gfa_in_meters, prediction (building clasification: Residential, Non-residential or Industrial), settlement_classification (formal or informal, for residential buildings). <br> • Wards (GIS layer with polygons). <br> • Wards with projected Population.|
| **Requirements** | it runs on local machine|
| **Outputs** | • Number of inhabitants per building footprint <br> • Number of inhabitants per building (informal weighted) <br> |

The process first calculates the Gross Floor Area (GFA) for all residential structures to serve as a capacity proxy. It then applies two parallel methodologies to distribute the population: a standard approach that allocates inhabitants strictly proportional to building size, and a weighted approach that applies a density multiplier to informal settlements to reflect higher occupancy rates. Both methods utilize an integer-constraint algorithm to assign whole numbers of people, ensuring precise alignment with official ward totals.


<a id="proximity-calc"></a>

### Distance Metrics / Proximity Calculations  
OBI leverages two different methodologies to compute distances. The first methodology computes point-to-point distance from each household to the closest water body or green area.

| Category | Details |
| :--- | :--- |
| **Input** | Building footprints, rasters (green/water coverage), OSM roads |
| **Requirements** | • **Air distance:** Runs on local machine.<br>• **OSRM:** Requires server setup. (See [repository](https://github.com/Open-Building-Insights/distance_metrics/tree/main)) |
| **Outputs** | • Closest Major Hospital (vehicle/foot)<br>• Closest First Contact Facility (vehicle/foot)<br>• Closest Green Area (>10,000m²)<br>• Closest Permanent Water Body |

It uses 4 notebooks that are described as follows:
* [Tree cover raster to polygon](https://github.com/Open-Building-Insights/distance_metrics/blob/main/air-distance/1.1_TreeCover_Raster_to_Polygon.ipynb) and [water raster to polygon](https://github.com/Open-Building-Insights/distance_metrics/blob/main/air-distance/2.1_Water_Raster_to_Polygon.ipynb) includes the URL from where the rasters were obtained. Downloading the rasters is manually done, afterwards, it takes as input the Area of Interest (a polygon of the City/Region) and clips out the raster. Finally, it converts them  from raster format to polygon, filtering out areas that are smaller than a given threshold.
* [Tree cover air-distance calculation](https://github.com/Open-Building-Insights/distance_metrics/blob/main/air-distance/1.2_TreeCover_AIR_Distance_calculation.ipynb) and [Water air-distance calculation](https://github.com/Open-Building-Insights/distance_metrics/blob/main/air-distance/2.2_Water_AIR_Distance_calculation.ipynb) compute the geodesic distance, this is the distance between the building footprint and the closest edge of the polygon. For each polygon points around the perimeter were computed at 50 meters step.
* The second methodology uses the OSRM (Open Source Routing Machine) to find the fastest route between coordinates. It calculates the time/distance from a building footprint to a Hospital or First/Aid center, thus it uses the road network from Open Street Map. The fastest route is calculated using two different kinds of profiles, car and pedestrian. It uses 4 notebooks described as follows:
    * [Cleaning dataset healthcenter](https://github.com/Open-Building-Insights/distance_metrics/blob/main/osrm/0.%20Cleaning_dataset_healthcenter.ipynb) includes the URL to download the information (for India) and cleans the dataset into two datasets: one that contains District Hospitals and a second that contains the remaining from the original sample, and are named as First Aid Centers. 
    * [Distance Health Centers to HH by Car](https://github.com/Open-Building-Insights/distance_metrics/blob/main/osrm/1.%20Distance%20Health_Centers%20to%20HH%20by%20Car.ipynb) computes four metrics, the distance and time for District Hospitals and First Aid centers (6 new columns: distance, time and name for each type of facility). It also returns a LOG FILE with information of the unroutable building footprints. 
    * [Distance Health Centers to HH by foot](https://github.com/Open-Building-Insights/distance_metrics/blob/main/osrm/2.%20Distance%20Health_Centers%20to%20HH%20by%20foot.ipynb) computes the same metrics as before but with a pedestrian profile.
    * [Fix not routable](https://github.com/Open-Building-Insights/distance_metrics/blob/main/osrm/3.%20Fix_not_routable.ipynb) it computes the missing metrics from the unroutable buildings by averaging values from neighboring building footprints. 


<a id="heat-exposure"></a>

### Heat exposure

In this workflow, Google Earth Engine (GEE) is used to calculate heat-exposure indicators based on the Humidex index.

| Category | Details |
| :--- | :--- |
| **Input** | • GEE account & project setup <br>• Building footprint dataset (with longitude, latitude) 
| **Requirements** | Runs on local machine|
| **Outputs** | • 4-band yearly raster <br>• 4 columns added to the building dataset (Extreme_Danger, Danger, Extreme_Caution, Caution) 


The script computes building-level heat exposure parameters based on the Humidex index, using ERA5-Land hourly reanalysis data. It processes one full calendar year (2024) of climate data to calculate daily maximum Humidex values, classifies each day into four heat-risk categories, aggregates these classifications into monthly and yearly heat-exposure rasters, and finally disaggregates the yearly results to OBI building footprints. The output is a set of building-level attributes describing how many days in the year each building is exposed to different levels of heat stress. The code used is available in the [Heat Exposure](https://github.com/Open-Building-Insights/heat_exposure/tree/main) repository. 


<a id="data-postprocessing"></a>

### Data Postprocessing
In this section, further processing is done on the OBI building dataset to add building types derived from OSM tags, add energy estimates, and mask sensitive buildings. The code used is available in the [Data Postprocessing](https://github.com/Open-Building-Insights/data-postprocessing/tree/main) repository. 

#### [1. Extracting OSM Tags](https://github.com/Open-Building-Insights/data-postprocessing/blob/main/notebooks/1.%20OSM_labeled_data_Parquet_Update.ipynb) 
| Category | Details |
| :--- | :--- |
| **Input** | •  Overpass API endpoint <br>• Building footprint dataset (with longitude, latitude, classification) 
| **Requirements** | Runs on local machine|
| **Outputs** | Classification disaggregation for non-residential and indsutrial buildings  

The notebook enriches an existing buildings dataset by adding OSM-derived building type labels. This is achieved by querying the **Overpass API** for tagged OSM features within the AOI, extracting results as **ways**, **relations**, and **nodes** for categories like schools, hospitals, commercial/industrial areas, offices, fuel stations, and other points of interest. Way/relation geometries are written as polygons, while node features are converted into small polygons for spatial matching. 



#### [2. Energy Estimates](https://github.com/Open-Building-Insights/data-postprocessing/tree/main/notebooks) [^1]
| Category | Details |
| :--- | :--- |
| **Input** | • Energy estimates (from OEMaps)  <br>• Building footprint dataset (with longitude, latitude, classification) 
| **Requirements** | Requires IBM COS and DB2 connection |
| **Outputs** | •  Electricity access percentage <br>• Electricity consumption (kWh/month) <br>• Standardized electricity consumption (kWh/month) 

There are two notebooks used to add electricity access and consumption estimates to the Kenya building dataset. The first notebook downloads tiled energy-estimation GeoJSONs, pulls the corresponding OBI buildings from IBM DB2 for the same tile extent, and performs a one-to-one spatial matching so each OBI footprint is linked to at most one energy-estimation footprint, saving the matched results as per-tile parquet outputs. The second notebook then takes those matched parquet files, standardizes the energy field names, and updates the IBM DB2 building table in parallel so each building record is enriched with the energy columns.

#### [3. Masking Sensitive Buildings](https://github.com/Open-Building-Insights/data-postprocessing/blob/main/notebooks/4.%20mask_buildings_parquet.ipynb) 

| Category | Details |
| :--- | :--- |
| **Input** | Building footprint dataset (with at least longitude, latitude, classification, OSM type) 
| **Requirements** | Runs on local machine |
| **Outputs** | Updated building dataset (with redacted attributes for millitary-related buildings)

This is a redaction step that protects sensitive records in the OBI buildings dataset. The notebook identifies buildings flagged as `osm_type = "Inactive"` (used for sensitive categories such as military-related features), and then masks them by clearing a set of non-public attributes. It also sets `classification = "Inactive"` for these buildings, which appear as gray inactive buildings on the [OBI platform](https://obi.sdg7energyplanning.org/).  


<a id="solar"></a>
### Solar Rooftop Potential [^a]

| Category | Details |
| :--- | :--- |
| **Input** | Building footprints, Digital Surface Model, if not available, Sentinel-2 L3C tiles. If not available, Sentinel-2 L1 tiles.|
| **Requirements** |An Intel-based instance with 24 vCPUs, 120 GiB RAM, and a single 48 GB NVIDIA L40S GPU for DSM inference.|
| **Outputs** | •Suitable area for solar panels <br>• Estimated generation in kwh/year <br>• Estimated capacity in kWp <br>• Estimated Costs of Installation ($USD) |

The assessment of Solar Rooftop Potential with GIS is not new. Several methodologies and platforms are available. However, there are a few of the limitations we have observed: Some high-level comprehensive methods exist, but are only available in a few locations and even when available, many of those are proprietary, limiting the availability of granular information to the general public. There are three main steps associated with the sizing of solar rooftop potential using Sentinel data, for further information click on each hyperlink:

* **[Step 1: Generating DSM:](https://github.com/Open-Building-Insights/UIEP-SRP/tree/main/dsm_creation)** Estimating the solar potential of rooftops i granular manner is a costly and time-consuming process. It requires high-granularity data, such as Digital Surface Models (DSMs), to accurately map urban morphology and accurately estimate the yield of incoming solar radiation. To address this, this project aims to train a U-Net convolutional neural network. This network is used to develop normalized Digital Surface Models (nDSM) from open-source Sentinel-2 satellite imagery, resampling – in the process – the data from 10 meters to 50-centimeter spatial resolution. 

* **[Step 2: Modelling solar potential using GRASS GIS:](https://github.com/Open-Building-Insights/UIEP-SRP/tree/main/grass_gis)** The resulting DSM works as input in GRASS GIS, a powerful open-source geospatial processing engine. Using its advanced modelling and time series analysis capabilities, the annual solar potential is then extracted for all rooftops in any area of interest, enabling access to this information across all geographies Sentinel-2 data is available.

* **[Step 3: Estimating suitable area and capacities:](https://github.com/Open-Building-Insights/UIEP-SRP/tree/main/suitable_area)** Suitable Rooftop areas are estimated using a Random Forest technique. Further parameters are computed and added as attributes in the final geojson file.

[^1]:We would like to acknowledge the [Open Energy Maps](https://www.openenergymaps.org) team (Dr. Stephen Lee from Massachusetts Institute of Technology (MIT) & Energy for Growth Hub, and Associate Professor Jay Taneja from the UMass Amherst) for providing building-level electricity access and consumption estimates and supporting with model evaluation metrics of our results in Kenya. 

[^a]:We would like to acknowledge Konstantine Müller et al. for their publication: "Deep Neural Network Regression for Normalized Digital Surface Model Generation With Sentinel-2 Imagery" in IEEE Journal of Selected Topics in Applied Earth Observations and Remote Sensing, vol. 16, pp. 8508-8519, 2023, [doi: 10.1109/JSTARS.2023.3297710](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=10189905). The generation of Digital Surface Models to estimate solar potential on rooftops using open-source datasets has been posible due to this work.