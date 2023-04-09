# Power-BI-Dashboarding-Project-NYC-Car-Accidents

### Project Inspiration 
-------------------------------------------------------------------------------------------------------------
I am deeply intrigued by the rise of the general population's interest to self-driving cars. 
Those who oppose autonomous vehicles emphasize the potential harm they might bring to the job availability of drivers
and to the environment, in terms of energy consumption.

Self-driving car enthusiasts argue that this new technology will have more positive effects than negative ones.
As these cars are algorithm-driven, equipped with sensors and cameras of their own that can detect other vehicles and can understand
traffic rules, they are better equipped to avoid accidents than humans.

According to the WHO, road mishaps cause 1.3 million deaths per year, with pedestrians, cyclists, and motorcyclists
being the top victims. It is also worth noting that road injuries are the leading cause of death for ages 5-29 (read more [here](https://www.who.int/news-room/fact-sheets/detail/road-traffic-injuries))

Time and time again, humanity has overcome the problem of jobs being obsolete. 
Although horse carriage drivers have been long gone, we have creatively come up with ways to 
bring about new jobs more suitable with our technology.

Will it be worth it for people to be more accepting of self-driving cars, if it means saving millions of lives? 
Or should we just first dig up on existing data, and identify variables/factors that we can improve on to lessen risks of car accidents?

### Data
-------------------------------------------------------------------------------------------------------------
The data is originally sourced [here](https://data.cityofnewyork.us/Public-Safety/Motor-Vehicle-Collisions-Crashes/h9gi-nx95). 

Here's a snippet of the raw data:

| CRASH_DATE | CRASH_TIME       | BOROUGH   | ZIP_CODE | LATITUDE         | LONGITUDE         | LOCATION               | ON_STREET_NAME | CROSS_STREET_NAME  | OFF_STREET_NAME | NUMBER_OF_PERSONS_INJURED | NUMBER_OF_PERSONS_KILLED | NUMBER_OF_PEDESTRIANS_INJURED | NUMBER_OF_PEDESTRIANS_KILLED | NUMBER_OF_CYCLIST_INJURED | NUMBER_OF_CYCLIST_KILLED | NUMBER_OF_MOTORIST_INJURED | NUMBER_OF_MOTORIST_KILLED | CONTRIBUTING_FACTOR_VEHICLE_1 | CONTRIBUTING_FACTOR_VEHICLE_2 | CONTRIBUTING_FACTOR_VEHICLE_3  | CONTRIBUTING_FACTOR_VEHICLE_4 | CONTRIBUTING_FACTOR_VEHICLE_5 | COLLISION_ID | VEHICLE_TYPE_CODE_1                 | VEHICLE_TYPE_CODE_2                 | VEHICLE_TYPE_CODE_3 | VEHICLE_TYPE_CODE_4 | VEHICLE_TYPE_CODE_5 |
|------------|------------------|-----------|----------|------------------|-------------------|------------------------|----------------|--------------------|-----------------|---------------------------|--------------------------|-------------------------------|------------------------------|---------------------------|--------------------------|----------------------------|---------------------------|-------------------------------|-------------------------------|--------------------------------|-------------------------------|-------------------------------|--------------|-------------------------------------|-------------------------------------|---------------------|---------------------|---------------------|
| 2019-01-27 | 15:40:00.0000000 | BROOKLYN  | 11236    | 40.645133972168  | -73.9023208618164 | (40.645134, -73.90232) | GLENWOOD ROAD  | ROCKAWAY PARKWAY   | NULL            | 0                         | 0                        | 0                             | 0                            | 0                         | 0                        | 0                          | 0                         | Following Too Closely         | Unspecified                   | NULL                           | NULL                          | NULL                          | 4069856      | Sedan                               | Sedan                               | NULL                | NULL                | NULL                |
| 2019-01-06 | 13:30:00.0000000 | MANHATTAN | 10016    | 40.7494163513184 | -73.9828720092773 | (40.749416, -73.98287) | NULL           | NULL               | 11              | EAST 36 STREET            | 0                        | 0                             | 0                            | 0                         | 0                        | 0                          | 0                         | 0                             | Oversized Vehicle             | Driver Inattention/Distraction | NULL                          | NULL                          | NULL         | 4284811                             | Tanker                              | Taxi                | NULL                | NULL                |
| 2019-01-15 | 16:45:00.0000000 | NULL      | NULL     | 40.7746124267578 | -73.9239883422852 | (40.774612, -73.92399) | 21 STREET      | ASTORIA PARK SOUTH | NULL            | 0                         | 0                        | 0                             | 0                            | 0                         | 0                        | 0                          | 0                         | Following Too Closely         | Unspecified                   | NULL                           | NULL                          | NULL                          | 4067800      | Station Wagon/Sport Utility Vehicle | Station Wagon/Sport Utility Vehicle | NULL                | NULL                | NULL                |




The ff. steps are used to transform the data using SQL (see SQL script [here](https://github.com/blumea7/Power-BI-Dashboarding-Project-NYC-Car-Accidents/tree/main/sql-scripts))


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
      - the effect of different time frames on the occurrence of accidents
      
Note: Feel free to add other interesting visuals that may bring about insightful information 


### Dashboard / Data Visualization
-------------------------------------------------------------------------------------------------------------
To follow...

### Power BI Notes 
-------------------------------------------------------------------------------------------------------------
To follow...
