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

## High Level Design -: A detailed design of the collaborative document editing service
![image](https://user-images.githubusercontent.com/22426280/235297184-6b3f8e6d-8628-4abb-9ee9-be99c2f9e005.png)








