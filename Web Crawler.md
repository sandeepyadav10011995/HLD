## WEB CRAWLER/ WEB INDEXER/ SPIDER

## Requirements
#### Functional Requirements
1. __Crawling__ -:
    1. The system should scour the WWW, spanning from a queue of seed URLs provided initially by the system administrator.
    2. Crawl rate -: The website which is being crawled should not be down down due to this(POLITENESS)
    3. Distributed Crawling
2. __DNS Query__ -: Inetrnal DNS
3. __Duplicate Detection__ -: Surveys suggests that more than 30 % of data on interenet is duplicate. We should be able to save space.
4. __Storing__: The system should be able to extract and store the content of a URL in a blob store. This makes that URL and its content processable by the search engines for indexing and ranking purposes.
5. __Scheduling__ -: Since crawling is a process that’s repeated, the system should have regular scheduling to update its blob stores’ records.

#### Non-Functional Requirements
1. __Scalability__: The system should inherently be distributed and multithreaded, because it has to fetch hundreds of millions of web documents.
2. __Consistency__: Since our system involves multiple crawling workers, having data consistency among all of them is necessary.
3. __Performance__: The system should be smart enough to limit its crawling to a domain, either by time spent or by the count of the visited URLs of that domain. This process is called self-throttling. The URLs crawled per second and the throughput of the content crawled should be optimal.
4. Improved user interface—__customized scheduling__


## Resource estimation
#### Assumptions
1. There are a total of 5 billion web pages.
2. The text content per webpage is 2070 KB.
3. The metadata for one web page is 500 Bytes.

#### Storage estimation
The collective storage required to store the textual content of 5 billion web pages is:
**Total storage per crawl = 5 Billion×(2070 KB+500B) = 10.35PB**

#### Traversal time
Assuming that the average HTTP traversal per webpage is 60 ms, the time to traverse all 5 billion pages will be:
Total traversal time=5 Billion×60 ms=0.3 Billion seconds= 9.5 years
**Number of servers estimation for multi-worker architecture**
1. No. of days required by 1 server to complete the task=9.5 years×365 days≈3468 days
2. If there are a n number of threads per server, we’ll divide 3,468 by n. 
3. For example, if one server is capable of executing ten threads at a time, then the number of servers is reduced to 3468/10 ≈ 347servers

#### Bandwidth estimation
Since we want to process 10.35PB of data per day the total bandwidth required would be:
10.35PB / 86400sec ≈ 120GB/sec ≈ **960Gb/sec**

960Gb/sec is the total required bandwidth. Now, assume that the task is distributed equally among 
3468 servers to accomplish the task in one day. Thus, the per server bandwidth would be:
960Gb/sec / 3468 server ≈ **277Mb/sec per server**

## Building Blocks
<img width="454" alt="Screenshot 2023-04-29 at 5 24 30 PM" src="https://user-images.githubusercontent.com/22426280/235301139-54404118-1891-4041-b102-bba6aef9d4f0.png">

## Components
1. The __HTML fetcher__ establishes a network communication connection between the crawler and the web hosts.
2. The __service host__ manages the crawling operation among the workers.
3. The __extractor__ extracts the embedded URLs and the document from the web page.
4. The __duplicate eliminator__ performs dedup testing on the incoming URLs and the documents.

#### Scheduler
1. A priority queue (URL frontier)
2. Relational database
3. DNS resolver
4. HTML fetcher : It has all the protocols logic to fetch the data.
5. Service host
6. Extractor
7. Duplicate eliminator : Can use Bloom filter for Membership Check.(1 - HIT Wonder)

## High Level Design : The overall web crawler design
![image](https://user-images.githubusercontent.com/22426280/235302125-3e9d0c28-57b7-4231-bf3b-84f7cd98c13a.png)
#### Updated Design 
1. HTML Fetcher -: New Protocols added
2. Extractor -: New file format processing added
<img width="949" alt="Screenshot 2023-04-29 at 5 54 44 PM" src="https://user-images.githubusercontent.com/22426280/235302404-8773b586-732c-42b3-9dbd-e3e1857d1828.png">

#### Questions
**What if one of the server goes down then how will we restore the state of the document**
__CHECKPOINTING__ : Snapshots happens at some freq in each server and the state is written to the disk

**The robots.txt file doesn’t protect crawlers from malicious or intended crawler traps. The other explained mechanisms must handle those traps.**

