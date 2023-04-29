# TypeAhead Generation

## Requirements
#### Functional requirements
1. The system should suggest top N (let’s say top ten) frequent and relevant terms to the user based on the text a user types in the search box.
2. Our proposed system should do the following:
    1. Provide suggestions based on the search history of the user.
    2. Store all the new and trending queries in the database to include them in the list of suggestions.
#### Non-functional requirements
1. __Low latency__: 
    1. The system should show all the suggested queries in real time after a user types. 
    2. The latency shouldn’t exceed 200 ms. 
    3. A study suggests that the average time between two keystrokes is 160 milliseconds. 
    4. So, our time-budget of suggestions should be greater than 160 ms to give a real-time response. 
    5. This is because if a user is typing fast, they already know what to search and might not need suggestions. 
    6. At the same time, our system response should be greater than 160 ms. 
    7. However, it should not be too high because in that case, a suggestion might be stale and less useful.
2. __Fault tolerance__: The system should be reliable enough to provide suggestions despite the failure of one or more of its components.
3. __Scalability__: The system should support the ever-increasing number of users over time.

## Resource estimation
**Google receives more than 3.5 billion searches every day**

#### Storage estimation
Assume that each query consists of 15 characters on average, and each character takes 2 Bytes of storage. 
According to this formulation, we would require the following:
2 billion × 15 × 2 = 60GB to store all the queries made in a day.
**Storage required per year: 60GB/day × 365 = 21.9TB/year**

#### Bandwidth estimation

15×3.5 billion = 52.5 characters per day.
Total read requests per second: 52.5B/86400 ≈ 0.607M characters/sec. 86,400 is the number of seconds per day.
Since each character takes 2 Bytes, the bandwidth our system would need is as follows: 0.607M×2×8 = 9.7Mb/sec
Therefore, the outgoing bandwidth requirement would become the following: 15×10×9.7Mb/sec = 1.46Gb/sec

#### Number of servers estimation
1. The system will suggest queries after each character a user types. Therefore, against 0.607 million characters per second, 
our system receives 0.607 million or 607,000 requests per second.
2. 607K/8000 ≈ 76servers

## Building Blocks
1. Load Balancers
2. Cache
3. Databases

## High Level Design
<img width="735" alt="Screenshot 2023-04-29 at 6 24 01 PM" src="https://user-images.githubusercontent.com/22426280/235303535-19af1dad-eb20-41d9-ba4c-f2c3d27a19b4.png">


## API Design
1. Get suggestion : getSuggestions(prefix) prefix : This refers to whatever the user has typed in the search bar.
2. Add trending queries to the database : addToDatabase(query) query : This represents a frequently searched query that crosses the predefined limit. 

## Data Structure For Storing Prefixes -: TRIE
#### Scaling Trie Data Structure -:
<img width="535" alt="Screenshot 2023-04-29 at 6 28 25 PM" src="https://user-images.githubusercontent.com/22426280/235303694-3478e3d8-d198-41e1-88d3-8be7a534aa28.png">

#### Update the Trie
<img width="936" alt="Screenshot 2023-04-29 at 6 29 56 PM" src="https://user-images.githubusercontent.com/22426280/235303763-8a722e9a-73d3-443c-8ad7-d115288a48b5.png">
1. We can put up a MapReduce (MR) job to process all of the logging data regularly, let’s say every 15 minutes. 
2. These MR services calculate the frequency of all the searched phrases in the previous 15 minutes and dump the results into a hash table in a database like Cassandra.

## Detailed Design
<img width="945" alt="Screenshot 2023-04-29 at 6 32 42 PM" src="https://user-images.githubusercontent.com/22426280/235303900-5df233b4-2af1-4c6e-8d44-01b9351d4920.png">

**Our design is divided into two main parts:**
1. A suggestion service
2. An assembler

#### Suggestion service
* At the same time that a user types a query in the search box, the getSuggestions(prefix) API calls hit the suggestions services. 
* The top ten popular queries are returned from the distributed cache, Redis.

#### Assembler
* __Collection service__: 
    * Whenever a user types, this service collects the log that consists of phrases, time, and other metadata and dumps it in a database that’s processed later. 
    * Since the size of this data is huge, the Hadoop Distributed File System (HDFS) is considered a suitable storage system for storing this raw data.

* __Aggregator__: 
    * The raw data collected by the collection service is usually not in a consolidated shape. 
    * We need to consolidate the raw data to process it further and to create or update the tries. 
    * An aggregator retrieves the data from the HDFS and distributes it to different workers. 
    * Generally, the MapReducer is responsible for aggregating the frequency of the prefixes over a given interval of time, and the frequency is updated periodically in the associated Cassandra database. 
    * Cassandra is suitable for this purpose because it can store large amounts of data in a tabular format.

* __Trie builder__:   
    * This service is responsible for creating or updating tries. 
    * It stores these new and updated tries on their respective shards in the trie database via ZooKeeper. 
    * Tries are stored in persistent storage in a file so that we can rebuild our trie easily if necessary. 
    * NoSQL document databases such as MongoDB are suitable for storing these tries. 
    * This storage of a trie is needed when a machine restarts.
    * The trie is updated from the aggregated data in the Cassandra database. 
    * The existing snapshot of a trie is updated with all the new terms and their corresponding frequencies. 
    * Otherwise, a new trie is created using the data in the Cassandra database.
    * Once a trie is created or updated, the system makes it available for the suggestion service.

