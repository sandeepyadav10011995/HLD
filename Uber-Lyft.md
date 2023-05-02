# UBER-LYFT HLD Design
## Introduction
* Uber is an application that provides ride-hailing services to its users. 
* Anyone who needs a ride can register and book a vehicle to travel from source to destination. 
* Anyone who has a vehicle can register as a driver and take riders to their destination. 
* Drivers and riders can communicate through the Uber app on their smartphones.

## Requirements
#### Functional Requirements
1. Update driver location. 
2. Find nearby drivers
3. Ride
    1. Rider selects PickUp point on map and can view ETA and price
    2. Rider pays to request a ride
    3. Drivers and Riders are matched; Driver accepts rides
    4. Rider receive info about Driver, Location and updates in real-time
4. Manage Payments
5. Update driver location
6. Find nearby drivers

#### Non-Functional Requirements
1. Availability
    * The system should be highly available. 
    * The downtime of even a fraction of a second can result in a trip failure, in the driver being unable to locate the rider, or in the rider being unable to contact the driver.
2. Scalability
    * The system should be scalable to handle an ever-increasing number of drivers and riders with time.
3. Reliability
    * The system should provide fast and error-free services. 
    * Ride requests and location updates should happen smoothly.
4. Consistency
    * The system must be strongly consistent. 
    * The drivers and riders in an area should have a consistent view of the system.
5. Fraud Detection -: Replicas of our databases.
    * The system should have the ability to detect any fraudulent activity related to payment.

## Resource Estimations
#### Assumptions
Let’s estimate the resources for our design. 
* 500 Million Riders
* 5 Million Drivers. 
* We’ll assume the following numbers for our estimates:
    * Daily Active Riders ~ 20 Million
    * Daily Active Drivers ~ 3 Million
    * Daily Trips ~ 20 Million Trips
    * All active drivers send a notification of their current location every four seconds.

#### Storage Estimations
##### Riders Metadata
* Assuming 1000 Bytes to store each rider's info such as Id, Name, Email, etc
* To store 500 Million Riders   
    * 500 * 10^6 * 1000 = 500 GB
* Additionally, if we have around 500,000 new riders registered daily, we’ll need a further 500 MB to store them.

##### Drivers Metadata
* Assuming 1000 Bytes to store each rider's info such as Id, Name, Email, etc
* To store 5 Million Riders   
    * 5 * 10^6 * 1000 = 5 GB
* Additionally, if we have around 100,00 new drivers registered daily, we’ll need around 100 MB to store them.

##### Trips Metadata
* Assuming 100 Bytes to store single trip information, including trip ID, rider ID, driver ID, and so on. 
* To store 20 million daily rides, we need
    * 20 * 10^6 * 100 = 2 GB

__Let’s calculate the total storage required for Uber in a single day:__
Storage for daily trips + Storage for new drivers + Storage for new riders + Storage required for drivers location
= 2GB + 500MB + 100MB + 180MB ~ 2.78GB

__Let’s calculate the total storage required for Uber for a year:__
= 2.78GB * 365 ~ 1.01 TB

#### Bandwidth Estimations
* We’ll only consider drivers’ location updates and trip data for bandwidth calculation since other factors don’t require significant bandwidth. 
* We have 20 million daily rides, which means we have approximately 232 trips per second.
* Now, each trip takes around 100 Bytes. So, it takes around 23 KB per second of bandwidth ~ 185 kbps
* As already stated, the driver’s location is updated every four seconds. If we receive the driver’s ID (3 Bytes) and location (16 Bytes), our application will take the following bandwidth: 3Million * (3+ 16) = 57MB * 8 ~ 114Mbps
* __Total bandwidth__ = 185kbps + 114Mbps ~ 114.19Mbps

#### Number of servers estimations
* DAU = 20 Million
* RPS of a server = 8000
* __Number of servers required__ = 20 * 10^6 / 8000 ~ 2500 servers

## Building Blocks
<img width="479" alt="Screenshot 2023-05-02 at 8 12 41 PM" src="https://user-images.githubusercontent.com/22426280/235700665-f9b4c30c-451a-4b34-84f1-67ff70ac4f5d.png">

 
## High Level Design

#### Trip Design
<img width="628" alt="Screenshot 2023-05-02 at 9 06 56 PM" src="https://user-images.githubusercontent.com/22426280/235715051-013c0516-f317-429f-bb6b-fc1fe7bb58e6.png">

![image](https://user-images.githubusercontent.com/22426280/235707357-b3841184-af80-46d2-a2b6-51fa37bec646.png)

## API Design
#### Update Driver Location
__API Call__ : updateDriverLocation(driverId, oldLat, oldLong, newLat, newLong)

#### Find nearby drivers
__API Call__ : findNearbyDrivers(riderId, lat, long)

#### Request A Ride
__API Call__ : requestRide(riderId, lat, long, dropOfflat, dropOffLong, typeOfVehicle)

#### Show Driver ETA
__API Call__ : showETA(driverId, eta)

#### Confirm PickUp
__API Call__ : confirmPickUp(driverId, riderId, timestamp)

#### Show Trip Updates
__API Call__ : showTripUpdates(tripId, riderId, driverId, driverLat, driverLong, timeElapsed, timeRemaining)

#### End Trip
__API Call__ : endTrip(tripId, riderId, driverId, timeElapsed, lat, long)

## Problem
* How to handle large number of users. 
* Lots of real-time data -: __HTTP Polling or Websockets__
* Serve Map images ? ==> Mapbox, Google Maps and many more..
* Convert Street Address to lat/long  ==> Mapbox, Google Maps and many more..
* Get Directions  ==> Mapbox, Google Maps and many more..
* Rider needs to pay; API needs to know when payment finishes processing
* Driver needs to get paid
* Uber uses Double Entry Book-Keeping
    * Double-entry bookkeeping, also known as, double-entry accounting, is a method of bookkeeping that relies on a two-sided accounting entry to maintain financial information. Every entry to an account requires a corresponding and opposite entry to a different account
    ![image](https://user-images.githubusercontent.com/22426280/235730507-d437a666-8ca9-4dd2-80e4-e6f87be8b6b6.png)
* Pricing varies by demand.
* Batch processing would be too slow
    ![image](https://user-images.githubusercontent.com/22426280/235731321-9217c9d8-6543-49d2-a869-b8ce7e60b23c.png)
* Needs to efficiently find drivers in an area.
* Needs to calculate ETA
    * Uber H3 -: Shard by Cell Id
    * Cell Id can be calculated from lat/long
    * One Cell Id can be used to calculate adjacent Cell Ids


#### Detailed Design
<img width="633" alt="Screenshot 2023-05-02 at 9 25 33 PM" src="https://user-images.githubusercontent.com/22426280/235719689-524aa150-ee1a-4118-935a-8860fda9b2eb.png">

##### Components
###### Location Manager
* The riders and drivers are connected to the location manager service.
* This service shows the nearby drivers to the riders when they open the application. 
* This service also receives location updates from the drivers every four seconds.
* The location of drivers is then communicated to the quadtree map service to determine which segment the driver belongs to on a map. 
* The location manager saves the last location of all drivers in a database and saves the route followed by the drivers on a trip.

###### Quadtree map service
* The quadtree map service updates the location of the drivers. 
* The main problem is how we deal with finding nearby drivers efficiently.
* A quadtree is a tree data structure in which each internal node has exactly four children. Quadtrees are the two-dimensional analog of octrees and are most often used to partition a two-dimensional space by recursively subdividing it into four quadrants or regions. The data associated with a leaf cell varies by application, but the leaf cell represents a “unit of interesting spatial information”.
* To overcome the above problem, we can use a hash table to store the latest position of the drivers and update our quadtree occasionally, say after 10–15 seconds. We can update the driver’s location in the quadtree around every 15 seconds instead of four seconds, and we use a hash table that updates every four seconds and reflects the drivers’ latest location. By doing this, we use fewer resources and time.
###### Request Vehicle
* The rider contacts the request vehicle service to request a ride. 
* The rider adds the drop-off location here. 
* The request vehicle service then communicates with the find driver service to book a vehicle and get the details of the vehicle using the location manager service.
* When a rider request a ride
    * Find closest driver using ride service
    * Tell driver API to accept or decline the ride.
    * Accepted ?
        * Notify rider
        * Driver gets the directions to pickup location.
    * Declined ?
        * Repeat with next closest driver.  

###### Find Driver
* The find driver service finds the driver who can complete the trip. 
* It sends the information of the selected driver and the trip information back to the request vehicle service to communicate the details to the rider. 
* The find driver service also contacts the trip manager to manage the trip information.

###### Trip Manager
* The trip manager service manages all the trip-related tasks. 
* It creates a trip in the database and stores all the information of the trip in the database.

###### ETA Service
* The ETA service deals with the estimated time of arrival. 
* It shows riders the pickup ETA when their trip is scheduled. 
* This service considers factors such as route and traffic. 
* The two basic components of predicting an ETA given an origin and destination on a road network are the following:
    * Calculate the shortest route from origin to destination.
    * Compute the time required to travel the route.


## Database
#### Storage Schema
* Riders
* Drivers
* Driver Location
* Trips
<img width="631" alt="Screenshot 2023-05-02 at 9 49 55 PM" src="https://user-images.githubusercontent.com/22426280/235725426-4f82fd0a-c6f8-4e6e-8aa2-6b04cb99b1ab.png">

##### Trips
![image](https://user-images.githubusercontent.com/22426280/235728615-1afded7f-ea4c-4078-a66a-464dc8e2a25d.png)

###### Global Indexing
* Global partitioned index contains keys from multiple table partitions in a single index partition. 
* This type of index is created using the GLOBAL clause during index creation. 
* A global index can be partitioned or non-partitioned (default).


