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
  * **Population:** Estimated inhabitance of residential buildings [based on wards census and projected population](https://github.com/Open-Building-Insights/socio-econometrics).

### 3. Proximity and Access
* **Healthcare Access**
  * **Closest Major Hospital by vehicle and foot (distance, travel time, name):** The name of the closest major hospital for the building using ground communication (roads, streets, sidewalks, etc.). The driving distance and walking distance is provided for the hospital both in kilometers and travel time.

  * **Closest First Contact Facility by vehicle and foot (distance, travel time, name):** The name of the closest health facility of any kind (dentists excluded) for the building using ground communication (roads, streets, sidewalks, etc.). The driving distance and walking distance is provided for the hospital both in kilometers and travel time.

* **Environment**
  * **Closest green area > 10,000m² (distance, area):** The distance to the closest significant forest or park in kilometers. The area of the greenery is also provided in hectares.

  * **Closest Permanent Water Body (distance, area):** The distance to the closest permanent water body in kilometers. The surface area of the water body is also provided in hectares.

* **Road Network**
  * **Road Network (distance):** Distance to the closest road of any kind given in kilometers.

  * **Primary Roads (distance):** Distance to the closest major road given in kilometers.

### 4. Heat Exposure: 
Open Building Insights provides the amount of dangerously hot days from the past year (2024), broken down into the following categories as defined by Humidex: 

* **Extreme Danger**: Number of days with temperature over 46 Celsius degrees.
* **Danger**: Number of days with temperature between 38 and 46 Celsius degrees.
* **Extreme Caution**: Number of days with temperature between 29 and 38 Celsius degrees.

### 5. Energy
* **Status**[^1]
  * **Electrification Status**: The estimated mean likelihood that a building of interest is connected to the electric grid.

  * **Electricity Consumption:**A mean-point estimate of a modeled distribution curve of monthly electricity consumption for a building, given in kWh.



* **Solar Rooftop Potential**
  * **Rooftop Suitable Area:** The estimated area on the roof of the building suitable to install solar panels given in square meters.

  * **Generation** and **Capacity:** The estimated generation over the year in kWh/year  and estimated capacity in kWp under the assumption, that the suitable area is covered by solar panels.

  * **Installation Cost:** Estimated installation cost of solar panels to cover the suitable roof area in dollars.


[^1]: Currently only available in Kenya. 
We would like to acknowledge the [Open Energy Maps](https://www.openenergymaps.org) team (Dr. Stephen Lee from Massachusetts Institute of Technology (MIT) & Energy for Growth Hub, and Associate Professor Jay Taneja from the UMass Amherst) for providing building-level electricity access and consumption estimates and supporting with model evaluation metrics of our results in Kenya.