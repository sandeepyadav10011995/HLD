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


