# Documentation
Open Building Insights is composed of two distinct functional blocks. The first consists of the Python scripts used to extract various [attributes](/documentation/list_attributes/README.md). The second is the [website](https://obi.sdg7energyplanning.org/about.html), which hosts processed information for India and Kenya that users can easily download. This documentation focuses on the data pipeline (see image below) used to enrich building footprints with these attributes.

## Suggested pipeline  
This section outlines the computational workflow used to obtain OBI datasets in India and Kenya. The scripts used are available on [GitHub](https://github.com/orgs/Open-Building-Insights/repositories), where each repository contains the relevant notebooks executed. These include: 
* Data preprocessing 	 
* Building classification 
* Informal settlement detection 
* Socio-economics 
* [Distance Metrics / Proximity Calculations](#proximity-calc)
* Heat exposure
Energy Estimates
* [Solar Rooftop Potential](#solar)
* Data Postprocessing 

![obi-workflow](/documentation/images/workflowv2.png)

### Data preprocessing
### Building classification 
### Informal settlement detection 
### Socio-economics 


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


### Heat exposure
### Data Postprocessing
### Energy Estimates [^1]

<a id="solar"></a>
### Solar Rooftop Potential [^a]
The assessment of Solar Rooftop Potential with GIS is not new. Several methodologies and platforms are available. However, there are a few of the limitations we have observed: Some high-level comprehensive methods exist, but are only available in a few locations and even when available, many of those are proprietary, limiting the availability of granular information to the general public. There are three main steps associated with the sizing of solar rooftop potential using Sentinel data, for further information click on each hyperlink:

* **[Step 1: Generating DSM:](https://github.com/Open-Building-Insights/UIEP-SRP/tree/main/dsm_creation)** Estimating the solar potential of rooftops i granular manner is a costly and time-consuming process. It requires high-granularity data, such as Digital Surface Models (DSMs), to accurately map urban morphology and accurately estimate the yield of incoming solar radiation. To address this, this project aims to train a U-Net convolutional neural network. This network is used to develop normalized Digital Surface Models (nDSM) from open-source Sentinel-2 satellite imagery, resampling – in the process – the data from 10 meters to 50-centimeter spatial resolution. 

* **[Step 2: Modelling solar potential using GRASS GIS:](https://github.com/Open-Building-Insights/UIEP-SRP/tree/main/grass_gis)** The resulting DSM works as input in GRASS GIS, a powerful open-source geospatial processing engine. Using its advanced modelling and time series analysis capabilities, the annual solar potential is then extracted for all rooftops in any area of interest, enabling access to this information across all geographies Sentinel-2 data is available.

* **[Step 3: Estimating suitable area and capacities:](https://github.com/Open-Building-Insights/UIEP-SRP/tree/main/suitable_area)** Suitable Rooftop areas are estimated using a Random Forest technique. Further parameters are computed and added as attributes in the final geojson file.

[^1]:We would like to acknowledge the [Open Energy Maps](https://www.openenergymaps.org) team (Dr. Stephen Lee from Massachusetts Institute of Technology (MIT) & Energy for Growth Hub, and Associate Professor Jay Taneja from the UMass Amherst) for providing building-level electricity access and consumption estimates and supporting with model evaluation metrics of our results in Kenya. 

[^a] We would like to acknowledge Konstantine Müller et al. for their publication: "Deep Neural Network Regression for Normalized Digital Surface Model Generation With Sentinel-2 Imagery" in IEEE Journal of Selected Topics in Applied Earth Observations and Remote Sensing, vol. 16, pp. 8508-8519, 2023, [doi: 10.1109/JSTARS.2023.3297710](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=10189905). The generation of Digital Surface Models to estimate solar potential no rooftopos using open-source datasets has been posible due to this work.