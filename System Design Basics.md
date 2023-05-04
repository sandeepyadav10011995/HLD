## Understanding the Basics of System Design

#### a. Key concepts and principles

##### Scalability
* The ability of a system to handle increasing amounts of load or traffic. 
* It’s important to understand different scalability approaches such as horizontal scaling (adding more machines to a system) and vertical scaling (adding more resources to a single machine).

##### Fault tolerance
* The ability of a system to continue functioning despite the failure of one or more of its components. 
* Techniques such as redundancy and load balancing can help increase a system’s fault tolerance.

##### Load balancing
* Load balancers distribute workloads across multiple machines in order to optimize resource usage and ensure that no single machine is overwhelmed.

##### Caching
* Storing frequently accessed data in a high-speed storage layer to reduce the load on the underlying data store and improve system performance.

##### Availability
* The ability of a system to respond to requests in a timely manner. 
* This is closely related to fault tolerance and is typically measured as a percentage of time that the system is operational.

##### Consistency
* The degree to which all nodes in a distributed system see the same data at the same time. 
* Consistency can be divided into different levels, such as strong consistency, eventual consistency, and no consistency.

##### Latency
* The time it takes for a request to be processed and a response to be returned. 
* Latency is an important factor in system design, particularly for systems that handle real-time data.

##### Throughput
* The number of requests a system can handle per unit of time. 
* Throughput is closely related to scalability and is often used as a measure of a system’s performance.

##### Partition Tolerance
* The ability of a system to continue functioning when network partitions occur. 
* In distributed systems, it’s impossible to have both consistency and partition tolerance at the same time, so a designer must decide which one is more important for the use case.

##### CAP Theorem
* The theorem states that it is impossible for a distributed system to simultaneously provide all three of the following guarantees: 
  * Consistency
  * Availability, and 
  * Partition Tolerance.

##### ACID Properties
* A set of properties that guarantee that database transactions are processed reliably. 
* The acronym stands for Atomicity, Consistency, Isolation, and Durability.

I**t’s important to be familiar with these concepts and understand how they apply to different types of systems.** 
* For example, a real-time financial trading system would need to have a high level of consistency and low latency. 
* While a social media platform might prioritize high availability and partition tolerance over consistency








