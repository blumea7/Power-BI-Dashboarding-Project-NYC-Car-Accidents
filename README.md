# Power-BI-Dashboarding-Project-NYC-Car-Accidents

### Project Introduction
-------------------------------------------------------------------------------------------------------------

According to the WHO, road mishaps cause 1.3 million deaths per year, with pedestrians, cyclists, and motorcyclists
being the top victims. It is also worth noting that road injuries are the leading cause of death for ages 5-29 (read more [here](https://www.who.int/news-room/fact-sheets/detail/road-traffic-injuries)). I decided to look into a dataset that could shed light on the factors affecting
the incidence of car accidents. 


### Data - Motor Vehicle Collisions 
-------------------------------------------------------------------------------------------------------------
The data is originally sourced [here](https://data.cityofnewyork.us/Public-Safety/Motor-Vehicle-Collisions-Crashes/h9gi-nx95). 

All the records in the Motor Vehicle Collisions dataset are from daily reports of NYPD. All car accidents that incurred injuries, deaths, or more than $1000 worth of damages are required to be filed into a police report (MV104-AN). There are collected records as early as 2012, and is continuosly updated on a daily basis up to this writing (April).  In total, there are 1.9 million records used in accomplishing the dashboard. All records for year 2023 are excluded as the records for the coming months are yet to be recorded. 


Here's a snippet of the raw data:

| CRASH_DATE | CRASH_TIME       | BOROUGH   | ZIP_CODE | LATITUDE         | LONGITUDE         | LOCATION               | ON_STREET_NAME | CROSS_STREET_NAME  | OFF_STREET_NAME | NUMBER_OF_PERSONS_INJURED | NUMBER_OF_PERSONS_KILLED | NUMBER_OF_PEDESTRIANS_INJURED | NUMBER_OF_PEDESTRIANS_KILLED | NUMBER_OF_CYCLIST_INJURED | NUMBER_OF_CYCLIST_KILLED | NUMBER_OF_MOTORIST_INJURED | NUMBER_OF_MOTORIST_KILLED | CONTRIBUTING_FACTOR_VEHICLE_1 | CONTRIBUTING_FACTOR_VEHICLE_2 | CONTRIBUTING_FACTOR_VEHICLE_3  | CONTRIBUTING_FACTOR_VEHICLE_4 | CONTRIBUTING_FACTOR_VEHICLE_5 | COLLISION_ID | VEHICLE_TYPE_CODE_1                 | VEHICLE_TYPE_CODE_2                 | VEHICLE_TYPE_CODE_3 | VEHICLE_TYPE_CODE_4 | VEHICLE_TYPE_CODE_5 |
|------------|------------------|-----------|----------|------------------|-------------------|------------------------|----------------|--------------------|-----------------|---------------------------|--------------------------|-------------------------------|------------------------------|---------------------------|--------------------------|----------------------------|---------------------------|-------------------------------|-------------------------------|--------------------------------|-------------------------------|-------------------------------|--------------|-------------------------------------|-------------------------------------|---------------------|---------------------|---------------------|
| 2019-01-27 | 15:40:00.0000000 | BROOKLYN  | 11236    | 40.645133972168  | -73.9023208618164 | (40.645134, -73.90232) | GLENWOOD ROAD  | ROCKAWAY PARKWAY   | NULL            | 0                         | 0                        | 0                             | 0                            | 0                         | 0                        | 0                          | 0                         | Following Too Closely         | Unspecified                   | NULL                           | NULL                          | NULL                          | 4069856      | Sedan                               | Sedan                               | NULL                | NULL                | NULL                |
| 2019-01-06 | 13:30:00.0000000 | MANHATTAN | 10016    | 40.7494163513184 | -73.9828720092773 | (40.749416, -73.98287) | NULL           | NULL               | 11              | EAST 36 STREET            | 0                        | 0                             | 0                            | 0                         | 0                        | 0                          | 0                         | 0                             | Oversized Vehicle             | Driver Inattention/Distraction | NULL                          | NULL                          | NULL         | 4284811                             | Tanker                              | Taxi                | NULL                | NULL                |
| 2019-01-15 | 16:45:00.0000000 | NULL      | NULL     | 40.7746124267578 | -73.9239883422852 | (40.774612, -73.92399) | 21 STREET      | ASTORIA PARK SOUTH | NULL            | 0                         | 0                        | 0                             | 0                            | 0                         | 0                        | 0                          | 0                         | Following Too Closely         | Unspecified                   | NULL                           | NULL                          | NULL                          | 4067800      | Station Wagon/Sport Utility Vehicle | Station Wagon/Sport Utility Vehicle | NULL                | NULL                | NULL                |




The ff. steps are used to transform the data using SQL (see script [here](https://github.com/blumea7/Power-BI-Dashboarding-Project-NYC-Car-Accidents/tree/main/sql-scripts))


    1. Renamed/simplified lengthy columns 
    2. Extracted the Weekday part of the date of accident ( 1 = Sunday, 2 = Monday, and so on). This will be used later in properly ordering the days, as Power BI by default displays days (and other strings) alphabetically in visuals. 
    3. Created a separate column describing which day the accident occurred (in DDD format).  
    4. Extracted the Month part of the date of accident ( 1 = January, 2 = February, and so on). This will be used later in properly ordering the month.
    5. Created a separate column describing which month the accident occurred (in MMM format)
    6. Created categories for the times at which the accident occurred (e.g., 1:40:00.0000000 is in 1 - 2 AM group)
    7. Dropped unnecessary columns
      
Here's a snippet of the transformed data:

| Collision ID | Crash Date | Day Key | Day | Month Key | Month | Year | Time             | Time Group | Town      | Zip Code | Latitude         | Longitude         | Street        | Crossing Street    | Off Street | Injured Persons | Killed Persons | Injured Pedestrians | Killed Pedestrians | Injured Cyclists | Killed Cyclists | Injured Motorists | Killed Motorists | Contributing Factor - Vehicle 1 | Contributing Factor - Vehicle 2 | Contributing Factor - Vehicle 3 | Contributing Factor - Vehicle 4 | Contributing Factor - Vehicle 6 | Vehicle 1                           | Vehicle 2                           | Vehicle 3 | Vehicle 4 | Vehicle 5 |
|--------------|------------|---------|-----|-----------|-------|------|------------------|------------|-----------|----------|------------------|-------------------|---------------|--------------------|------------|-----------------|----------------|---------------------|--------------------|------------------|-----------------|-------------------|------------------|---------------------------------|---------------------------------|---------------------------------|---------------------------------|---------------------------------|-------------------------------------|-------------------------------------|-----------|-----------|-----------|
| 4069856      | 2019-01-27 | 1       | Sun | 1         | Jan   | 2019 | 15:40:00.0000000 | 3 - 4 PM   | BROOKLYN  | 11236    | 40.645133972168  | -73.9023208618164 | GLENWOOD ROAD | ROCKAWAY PARKWAY   | NULL       | 0               | 0              | 0                   | 0                  | 0                | 0               | 0                 | 0                | Following Too Closely           | Unspecified                     | NULL                            | NULL                            | NULL                            | Sedan                               | Sedan                               | NULL      | NULL      | NULL      |
| 4284811      | 2019-01-06 | 1       | Sun | 1         | Jan   | 2019 | 13:30:00.0000000 | 1 - 2 PM   | MANHATTAN | 10016    | 40.7494163513184 | -73.9828720092773 | NULL          | NULL               | 11         | EAST 36 STREET  | 0              | 0                   | 0                  | 0                | 0               | 0                 | 0                | 0                               | Oversized Vehicle               | Driver Inattention/Distraction  | NULL                            | NULL                            | NULL                                | Tanker                              | Taxi      | NULL      | NULL      |
| 4067800      | 2019-01-15 | 3       | Tue | 1         | Jan   | 2019 | 16:45:00.0000000 | 4 - 5 PM   | NULL      | NULL     | 40.7746124267578 | -73.9239883422852 | 21 STREET     | ASTORIA PARK SOUTH | NULL       | 0               | 0              | 0                   | 0                  | 0                | 0               | 0                 | 0                | Following Too Closely           | Unspecified                     | NULL                            | NULL                            | NULL                            | Station Wagon/Sport Utility Vehicle | Station Wagon/Sport Utility Vehicle | NULL      | NULL      | NULL      |


### Problem Statement
-------------------------------------------------------------------------------------------------------------
Create a dashboard that will give a brief overview of:

      - the leading causes of vehicular accidents in NYC
      - the types and quantity of vehicles involved in the accidents
      - the most dangerous streets for motorists, cyclists, and pedestrians
      - occurrence of accidents plotted against different timeframes
      
Note: Feel free to add other interesting visuals that may bring about insightful information 


### Dashboards
-------------------------------------------------------------------------------------------------------------
#### Overview
![Dashboard Overview](https://github.com/blumea7/Power-BI-Dashboarding-Project-NYC-Car-Accidents/blob/main/assets/Dashboard%20Overview.JPG)

##### Insights
    1. 679,000 records have unspecified reason for collision
            - to improve the data, it is recommended that the NYPD improves on taking note of the causes of collision
            
    2. All identified major causes of car collisions in NYC are caused by poor driving habits or human error.
            - Law makers could look into their existing laws and find possible points of improvements 
              in providing stricter rules, fines, and implementation.
    
    3. Majority of injuries caused by car collisions involve motorists (72.81%),
       while majority of fatalities involve both pedestrians (51.52%) and motorists (41%). 
    
    4. The top 10 most dangerous streets are identified; There is a visual slicer so that streets 
       that are dangerous to specific types of non-vehiclar road users (pedestrians, cyclists, motorists) 
       could be identified. This is done so local city planners and law makers could specifically determine what 
       kind of improvement to perform on different streets.
       
            For streets dangerous to pedetrians, the ff. can be explored:
                - installation of walk-way road barriers
                - addition and/or proper placement of pedestrian lanes
                - usage of underpass or overpass
                
            For streets dangerous to cyclists, the ff. can be explored:
                - bike lane design 
                - road impairments, holes, and other damages
                - bike lane-road barriers
                - for busy streets, heavy-duty barriers should be used

                
             Other points of improvement that can be explored:
                - addition of traffic personnel
                - review existing traffic control systems
                - review of existing speed limit
                    
    5. Most car accidents involve 2 vehicles, but those involving only 1 vehicle caused the most number of deaths. 
            - actually it is established that the lower the number of involved vehicle, 
              the higher the number of deaths, and vice versa.
            
               
#### Time Factors
![Time Factors](https://github.com/blumea7/Power-BI-Dashboarding-Project-NYC-Car-Accidents/blob/main/assets/Time%20Factors.JPG)


    1. The number of car accidents follow a downward trend since 2018
            - possibly because of impositon of lower speed limits during that year
            
    2. There was an abrupt decline of car accidents in 2020 because of the COVID-19 pandemic.
            - the trend did not recover since then, possibly because of the adaptation of workspaces to hybrid setup
            - it is recommended that local law makers look into promoting fully work-from-home / hybrid work setups
            
    3. Usually, number of recorded collisions peak at Fridays.
    
    4. On an hourly basis, the number of car accidents starts to rise by 6-7 AM and peaks at 4-5 PM. 
            - It is recommended that the LGU encourage office managements to give flexible rules for 
              clocking in and clocking out so that inflow of cars can be distributed across a wider time range. 
            
            
### Power BI Notes 
-------------------------------------------------------------------------------------------------------------
#### Data Model

![Data Model](https://github.com/blumea7/Power-BI-Dashboarding-Project-NYC-Car-Accidents/blob/main/assets/Data%20Model.JPG)

Notes: 

        1. Originally, there was only one table which contains the raw dataset (NYC Accident). 
        2. Stacked Chart Legend Order and Time order Tables were created to customize the sorting 
           order of the stacked column chart and the hour group labels in the Time Factors Dashboard
        3. Casualty Classification and Involved People Talbes were created to create
           the Top 10 Most Dangerous Streets Visual in the Overview Dashboard
        4. Base Measures table contains the custom measures and calculations used to display needed in the visuals.
  
#### Custom Sorting of Stacked Column Chart
To follow... 

#### Using DAX to create the Top 10 Most Dangerous Streets Visual
To follow... 

