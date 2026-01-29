## Data Identification & Characteristics
This is an extended list of the attributes available in OBI.

### 1. Identification
* **Coordinates**
  * Coordinates refers to the coordinates of the center of the building provided as **latitude** and **longitude** using the EPSG:4326 reference system
* **Source**
  * **Footprint Source** and **Footprint Source Confidence** refers to where the data was obtained and tis confidence. If it is from Microsoft, it refers to Microsoft's Building Footprints, while OSM refers to OpenStreetMapCraglia et al. (2003). In case the building footprint was obtained from Google's V3 Open Buildings the confidence of their model is provided.

* **Classification Type:** Building footprints are classified into residential, non-residential and industrial indicating their use based on our methodology.
    * **Footprint Classification**:  Buildings which are associated with OSM buildings are classified based on the available OSM information, in which case the source is *OSM Derived*. For the rest of the buildings, their types are determined by one of the [machine learning models](https://github.com/Open-Building-Insights/new-classification-model) created whitin the OBI project, in which case **Classification Source** provides the name of the model and it the **Classification Confidence** is provided for its choice.   

* **Informal Status and Confidence:**
Urban areas are searched by a [machine learning model specialized in finding areas](https://github.com/Open-Building-Insights/informal-settlement-detection), which are possibly informal. Areas find by the model are annotated as Informal and the model's confidence is provided, the rest of urban areas are annotated as Formal and for such areas, which are less urbanized and therefore the model is not executed are annotated by the text Not Applicable and are considered formal for visualization purposes.


### 2. Characteristics
* **Structure**
  * **Height** and **Floors**: The height of the building is provided in meters as well as in number of floors. The first floor is assumed to be 4.5 meters tall to accommodate higher basements or other elevation of the building, all other floors are assumed to be 3 meters tall.
  * **Perimeter:** The total length of the outer walls of the building given in meters.
  * **Building Faces:** The number of outer walls (faces) of the building.
  * **Roof Area:** Roof area refers to the area of the building when viewed from top given in square meters.

  * **Gross Floor Area:** Gross floor area is the total of footprint areas of each floor on the building. Computed as footprint area times the amount of floors for the building provided as square meters.

  * **GHS-SMOD:** refers to degree of urbanization as defined and categorized by Global Human Settlement Layer.

* **Urban Morphology:** 
  
  * **Elevation:** The elevation of ground on which the building was constructed given in meters.

  * **Building Density:** The amount of buildings within a 100 meter square centered around the building in question.

  * **Built Up Volume:** The total volume of buildings averaged within a 100 meter square centered around the building in question given in m³/m².

  * **Fabric Density Ratio:** The facade density withing a 100 meter square centered around the building in question.

* **Demographics**
  * **Population:** Estimated inhabitance of residential buildings.

### 3. Heat Exposure
* Extreme Danger
* Danger
* Extreme Caution

### 4. Proximity and Access
* **Healthcare Access**
  * Closest Major Hospital by vehicle and foot (distance, travel time, name)
  * Closest First Contact Facility by vehicle and foot (distance, travel time, name)
* **Environment**
  * Closest green area > 10,000m² (distance, area)
  * Closest Permanent Water Body (distance, area)
* **Road Network**
  * Road Network (distance)
  * Primary Roads (distance)

### 5. Energy
* **Status**
  * Electrification Status
  * Electricity Demand
  * Electricity Data Source
* **Solar Rooftop Potential**
  * Rooftop Suitable Area
  * Generation
  * Capacity
  * Installation Cost
        