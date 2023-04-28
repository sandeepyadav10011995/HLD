**Clarifying Questions?**
1. Are we designing the Google Search we know that we use or a particular part of it?
2. Search Query and Crawler?
3. Only Text or Image or Video?
4. All languages or only English?
5. How long we can expect that page content can change should I go ahead with an assumption or you have in you mind?



**Design Google Search**
DAU -: 2 billion
Webpages to crawl -: 100 Billion
Frequency to Update Pages
        - Most -: every two weeks
        - Fast Changing -: Once every day
        - News -: Provide APIs for them to notify us

**Non-Functional Requirements** -: 
Latency -: 
        Hot Searches -: Give users the results in seconds
        For Corner Case Searches -: <1 minute
Consistency -: Eventual Consistency

Concurrency -: High


**Estimates** -:
Search side -2 billion * 5 searches per user = 10 billion * 24 * 3600 ~ 150 K QPS
Crawler side -: 100billion/14 * 24 * 3600 ~ 150K * 10/14 ~ 100K QPS 


![image](https://user-images.githubusercontent.com/22426280/234954678-6ee746c4-3fdc-4ef7-b759-be52107af757.png)




