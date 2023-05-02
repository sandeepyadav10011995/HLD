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
5. Fraud Detection
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

 



