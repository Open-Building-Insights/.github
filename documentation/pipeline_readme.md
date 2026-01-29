# Documentation
Open Building Insights is composed of two distinct functional blocks. The first consists of the Python scripts used to extract various [attributes](/documentation/list_attributes/README.md). The second is the [website](https://obi.sdg7energyplanning.org/about.html), which hosts processed information for India and Kenya that users can easily download. This documentation focuses on the data pipeline (see image below) used to enrich building footprints with these attributes.

## Suggested pipeline  
This section outlines the computational workflow used to obtain OBI datasets in India and Kenya. The scripts used are available on [GitHub](https://github.com/orgs/Open-Building-Insights/repositories), where each repository contains the relevant notebooks executed. These include: 
* Data preprocessing 	 
* Building classification 
* Informal settlement detection 
* Socio-economics 
* Distance Metrics / Proximity Calculations  
* Heat exposure
Energy Estimates
* Solar Rooftop Potencial
* Data Postprocessing 

![obi-workflow](/documentation/images/workflowv2.png)

### Data preprocessing
### Building classification 
### Informal settlement detection 
### Socio-economics 

### Distance Metrics / Proximity Calculations  
OBI leverages two different methodologies to compute distances. The first one computes point-to-point distance from each household to the closest water body/green area.

* **Input:** Building footprints, rasters (for green and water coverage) and OSM roads for distance to health facilities calculation
* **Requirements:** For air distance the computations could run on a local machine. For OSRM computations, a server needs to be set-up. For more detailes check the [repository](https://github.com/Open-Building-Insights/distance_metrics/tree/main).
* **Outputs:**
    * Closest Major Hospital by vehicle and foot (distance, travel time, name).
    * Closest First Contact Facility by vehicle and foot (distance, travel time, name).
    * Closest green area > 10,000m² (distance, area)
    * Closest Permanent Water Body (distance, area)

It uses 4 notebooks that are described as follows:
* [Tree cover raster to polygon](https://github.com/Open-Building-Insights/distance_metrics/blob/main/air-distance/1.1_TreeCover_Raster_to_Polygon.ipynb) and [water raster to polygon](https://github.com/Open-Building-Insights/distance_metrics/blob/main/air-distance/2.1_Water_Raster_to_Polygon.ipynb) includes the URL from where the rasters were obtained. Downloading the rasters is manually done, afterwards, it takes as input the Area of Interest (a polygon of the City/Region) and clips out the raster. Finally, it converts them  from raster format to polygon, filtering out areas that are smaller than a given threshold.
* [Tree cover air-distance calculation](https://github.com/Open-Building-Insights/distance_metrics/blob/main/air-distance/1.2_TreeCover_AIR_Distance_calculation.ipynb) and [Water air ditance calculation](https://github.com/Open-Building-Insights/distance_metrics/blob/main/air-distance/2.2_Water_AIR_Distance_calculation.ipynb) compute the geodesic distance, this is the distance between the building footprint and the closest edge of the polygon. For each polygon points around the perimeter were computed at 50 meters step.
* The second methodology uses the OSRM (Open Source Routing Machine) to find the fastest route between coordinates. It calculates the time/distance from a building footprint to a Hospital or First/Aid center, thus it uses the road network from Open Street Map. The fastest route is calculated using two different kinds of profiles, car and pedestrian. It uses 4 notebooks described as follows:
0. [Cleaning dataset healthcenter](https://github.com/Open-Building-Insights/distance_metrics/blob/main/osrm/0.%20Cleaning_dataset_healthcenter.ipynb) includes the URL to download the information (for India) and cleans the dataset into two datasets: one that contains District Hospitals and a second that contains the remaining from the original sample, and are named as First Aid Centers. 
1. [Distance Health Centers to HH by Car](https://github.com/Open-Building-Insights/distance_metrics/blob/main/osrm/1.%20Distance%20Health_Centers%20to%20HH%20by%20Car.ipynb) computes four metrics, the distance and time for District Hospitals and First Aid centers (6 new columns: distance, time and name for each type of facility). It also returns a LOG FILE with information of the unroutable building footprints. 
2. [Distance Health Centers to HH by foot](https://github.com/Open-Building-Insights/distance_metrics/blob/main/osrm/2.%20Distance%20Health_Centers%20to%20HH%20by%20foot.ipynb) computes the same metrics as before but with a pedestrian profile.
3. [Fix not routable](https://github.com/Open-Building-Insights/distance_metrics/blob/main/osrm/3.%20Fix_not_routable.ipynb) it computes the missing metrics from the unroutable buildings by averaging values from neighboring building footprints. 


### Heat exposure
### Data Postprocessing
### Energy Estimates
### Solar Rooftop Potencial
 