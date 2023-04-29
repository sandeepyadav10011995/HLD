## Facts About Google Doc -: 

#### A collaborative document editing service can be designed in two ways:
1. It could be designed as a centralized facility using client-server architecture to provide document editing service to all users.
2. It could be designed using peer-to-peer technology to collaborate on a single document.

#### Note: According to a survey, 64% of people use Google Docs for document editing at least once a week.

## Requirements
#### Functional Requirements -:
1. Document collaboration: Multiple users should be able to edit a document simultaneously. Also, a large number of users should be able to view a document.
2. Conflict resolution: The system should push the edits done by one user to all the other collaborators. The system should also resolve conflicts between users if they’re editing the same portion of the document.
3. Suggestions: The user should get suggestions about completing frequently used words, phrases, and keywords in a document, as well as suggestions about fixing grammatical mistakes.
4. View count: Editors of the document should be able to see the view count of the document.
5. History: The user should be able to see the history of collaboration on the document.
6. File Upload/Download
7. Share the file
8. You should be able to restore the versions

#### Non-Functional Requirements -:
1. Latency: Different users can be connected to collaborate on the same document. Maintaining low latency is challenging for users connected from different regions.
2. Consistency: The system should be able to resolve conflicts between users editing the document concurrently, thereby enabling a consistent view of the document. At the same time, users in different regions should see the updated state of the document. Maintaining consistency is important for users connected to both the same and different zones.
3. Availability: The service should be available at all times and show robustness against failures.
4. Scalability: A large number of users should be able to use the service at the same time. They can either view the same document or create new documents 
5. How will you optimize for storage on server side.

## Estimations
#### Resource Estimation
1. We assume that there are 80 million daily active users (DAU).
2. The maximum number of users able to edit a document concurrently is 20.
3. The size of a textual document is 100 KB.
4. Thirty percent of all the documents contain images, whereas only 2% of documents contain videos.
5. The collective storage required by images in a document is 800 KB, whereas each video is 3 MB.
6. A user creates one document in one day.

#### Storage Estimation
<img width="901" alt="Screenshot 2023-04-29 at 3 33 47 PM" src="https://user-images.githubusercontent.com/22426280/235297042-fa345265-12fc-4975-af26-63387140dc06.png">
##### The total storage required for one day is as follows: 8 + 19.2 + 4.8 = 32 TBs per day

#### Storage Estimation
![image](https://user-images.githubusercontent.com/22426280/235297062-3a6bea6c-af91-4294-bb69-1c6605f5e79b.png)

##### Note: The total bandwidth required is equal to the sum of incoming and outgoing traffic. = 3+14.7 ≈ 18Gbps approximately.

#### Number of servers estimation
Number of daily active users/ Queries handled per second =  80M /8000 = 10,000 servers

## Building Blocks    
![image](https://user-images.githubusercontent.com/22426280/235297151-28eed9b3-34f2-4a59-b78a-91f949958b25.png)

## Components
1. __API gateway__ -: Edit requests, comments on a document, notifications, authentication, and data storing requests will all go through the API gateway.
2. __Application servers__ -: The application servers will run business logic and tasks that generally require computational power. For instance, some documents may be converted from one file type to another (for example, from a PDF to a Word document) or support features like import and export. It’s also central to the attribute collection for the recommendation engine
3. __Data stores__ -: 
      1. __NoSQL__ for storing user comments for quicker access
      2. To save the edit history of documents, we can use a __time series database__. 
      3. We’ll use __blob storage__ to store videos and images within a document.
      4. Finally, we can use distributed cache like __Redis__ and a CDN to provide end users good performance.
      5. We use __Redis__ specifically to store different data structures, including user sessions, features for the typeahead            service, and frequently accessed documents. 
      6. The __CDN__ stores frequently accessed documents and heavy objects, like images and videos.
4. __Processing queue__ -: Since document editing requires frequently sending small-sized data (usually characters) to the server, it’s a good idea to queue this data for periodic batch processing. We’ll add characters, images, videos, and comments to the processing queue.
5. We’ll manage document access privileges through the session servers. Essentially, there will also be configuration, monitoring, pub-sub, and logging services that will handle tasks like monitoring and electing leaders in case of server failures, queueing tasks like user notifications, and logging debugging information.

## High Level Design -: A detailed design of the collaborative document editing service
![image](https://user-images.githubusercontent.com/22426280/235297184-6b3f8e6d-8628-4abb-9ee9-be99c2f9e005.png)

## Workflow
1. __Asynchronous operations__: Notifications, emails, view counts, and comments are asynchronous operations that can be queued through a pub-sub component like Kafka. The API gateway generates these requests and forwards them to the pub-sub module. Users sharing documents can generate notifications through this process.
2. __Suggestions__: Suggestions are in the form of the typeahead service that offers autocomplete suggestions for typically used words and phrases. The typeahead service can also extract attributes and keywords from within the document and provide suggestions to the user. Since the number of words can be high, we’ll use a NoSQL database for this purpose. In addition, most frequently used words and phrases will be stored in a caching system like Redis.
3. __Import and export documents__: The application servers perform a number of important tasks, including importing and exporting documents. Application servers also convert documents from one format to another. For example, a .doc or .docx document can be converted in to .pdf or vice versa. Application servers are also responsible for feature extraction for the typeahead service.

## Storage Optimization
![image](https://user-images.githubusercontent.com/22426280/235298221-0d671e73-a0cc-4847-b016-05d9becbd167.png)

## High Level Design Of the Client Side
**In this client is also going to play the major role !!**

![image](https://user-images.githubusercontent.com/22426280/235298574-b9208123-5e8b-4a48-8dc0-57bca7771463.png)

1. __INTERNAL METADATA__ -: Maintains chunks,. sequence, file, folder etc.
2. __CHUNKER__ -: Chunks the file(Split or merge)
3. __WATCHER__ -: Listens to the remote changes by syncronization services and sync changes on server side. Monitors the local changes as well.  __Notifies Indexer__
4. __INDEXER__ -: Process the events received from WATCHER.
      #### EVENTS -: 
      1. __SYNC EVENTS(Server to Local)__
      2. __LOCAL TO SERVER__
      
      #### Steps
      1. Update inetrnal metadata db stc with chunks of modified files __CHANGES COMING FROM SERVER !!__
      2. __If Local Changes__ -: __Indexer__ communicates back with SYNC Service(Which will broadcast to the other connected clients and also updates remote metadata db.) 
      
      
## Messaging Service
![image](https://user-images.githubusercontent.com/22426280/235299192-a6b82f2f-66a0-4de4-8865-31b9581d3ca2.png)

#### Why there is separate response queue for each client and only one request queue?
__Reasons__ -: 
1. Every client is not online all the time.
2. Request Queue is one because a client can send the request only when he/she is online/alive and they have done remote changes and want to pull when online.

## Block Service
Puts the changes in S3
File -: training.doc
        10 chunks
        chunk_checksum, doc_id, location, user, version, timestamp, sequence
__Note__ -: 
1. Every chunk is differentiated using CHECKSUM -: Takes the optimization on next level.
2. Data Deduplication -: On server, if we have chunk with similar hash(even from other user); we don't need to create another copy.
            
## Data Syncronization
1. Event Parsing (Character or String wise)
2. Diff SYNC

__QUESTION__ -: What does the google doc use from the above two startegies?
1. Since websockets are not COST Effective for Google.
2. That is we use Event Parsing --> via HTTP Parsing
      1. Insert
      2. Delete
      3. Change
     
__Problem__ -: RACE CONDITION :: This is more like merge conflict in GIT. Solution is -:
1. Opeartional Transaformation
2. CRDT





      
                  











