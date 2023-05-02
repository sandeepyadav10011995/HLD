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




