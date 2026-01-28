## Data Identification & Characteristics
This is an extended list of the attributes available in OBI.

### 1. Identification
* **Coordinates**
  * Coordinates refers to the coordinates of the center of the building provided as **latitude** and **longitude** using the EPSG:4326 reference system
* **Source**
  * **Footprint Source** and **Footprint Source Confidence** refers to where the data was obtained and tis confidence. If it is from Microsoft, it refers to Microsoft's Building Footprints, while OSM refers to OpenStreetMapCraglia et al. (2003). In case the building footprint was obtained from Google's V3 Open Buildings the confidence of their model is provided.

* **Classification Type:** Building footprints are classified into residential, non-residential and industrial indicating their use based on our methodology.
    * **Footprint Classification**:  Buildings which are associated with OSM buildings are classified based on the available OSM information, in which case the source is *OSM Derived*. For the rest of the buildings, their types are determined by one of the (machine learning models)[https://github.com/Open-Building-Insights/new-classification-model] created whitin the OBI project, in which case **Classification Source** provides the name of the model and it the **Classification Confidence** is provided for its choice.   

* **Informal Status and Confidence:**
Urban areas are searched by a machine learning model specialized in finding areas, which are possibly informal. Areas find by the model are annotated as Informal and the model's confidence is provided, the rest of urban areas are annotated as Formal and for such areas, which are less urbanized and therefore the model is not executed are annotated by the text Not Applicable and are considered formal for visualization purposes.


* **Informal Status**
  * Informal Status
  * Informal Status Confidence

### 2. Characteristics
* **Structure**
  * Height
  * Perimeter
  * Building Faces
  * Roof Area
  * Gross Floor Area
* **Urban Morphology**
  * GHS-SMOD
  * Elevation
  * Building Density
  * Built Up Volume
  * Fabric Density Ratio
* **Demographics**
  * Population

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
        