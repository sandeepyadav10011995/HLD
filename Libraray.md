# Design system for a library to manage their books
#### Basic functionalities:
* Librarians want to be able to track information about books leg., title, author, I publication dates, patrons leg, name, address), and which patrons have which books checked out.
* usual checkout / retum workflow
#### Handling for special books:
* When a special book is returned by a patron, the librarian puts the book into a scanning machine that takes a few high resolution photos of various parts of the book (described in more detail below). 
* These images are used to create a condition report" for the book.
* The condition report shows the images and patron name from the last 10 returns for a given book. 
* This lets libraries visually inspect the state of a book over time. 
* E.g. If a librarian notices gum on the cover, I they can refer to the condition report to determine which patron returned the book with gum on it.

#### webserver & webhook
* The scanner has a tiny built-in webserver. 
* The urls would be served by the webserver and they do pointito images saved in internal storage
* For the webhook, you can think it as a notification where the app can register an API to receive that notification. 
* And scanner is going to send a post request to that API when scanning finishes.

#### Condition reports
* Condition reports take on the order of minutes to generate, due to the number of high resolution images present and extra fancy image filtering and analysis. 
* Librarians, being busy people, don't want to wait for a condition report to be created on demand every time it's requested.
* The system should generate the condition report as soon as possible so it's available to view or donnioad.

#### Basic Info
* no private cloud, 3 servers max for the system
* availability requirement: don't have to be HA, but needs to be correct can afford several minutes breakage when system reboots
#### Success metrics:
* % of condition reports coverage on special books
* Failure rate of report generation on special book returns + Average processing tiem for the checkout/retur
#### Functional
* regular checkout & return process
* browse & search for a book
* browse history on a book / patron + dashboard
* scanning special books and generating reports
Concurrency
Daily transactions:
* 100k patrons
* 1 book per month per patron
* 100k / 30 = 4k per day = 500 per hour a 8 transactions per minute * 2 = 16 per minute
* peaks: 3 + 16 = 50 per minute
* 20 stations, 6 always open, and 14 for peaks + 3 transactions per minute per station for peak
* for average: 3 transaction per minute
#### Non-Functional
* Availablity: non-HA consistency: eventual capacity:
* DB -: Obj storage

## High-Level Structure
![Screenshot 2023-05-08 at 11 24 26 AM](https://user-images.githubusercontent.com/22426280/236746706-6d7c5f73-39eb-414c-a690-b80f8fbe1689.png)

<img width="733" alt="Screenshot 2023-05-08 at 11 25 13 AM" src="https://user-images.githubusercontent.com/22426280/236746641-6e75cec1-e306-4b87-a924-671bb3181b8a.png">

#### Disaster recovery:
###### Atomic operations:
* DB & message queue, maintain state over reboot
* Hand scanner
###### Image scanner: 
* it's going to finish once loading a book
* send out the notificaiton (webhook)
###### Valid state:
* special book returnsi only valid after scanner sends us the notification (urls of images)
* if crashes before receiving he notification re-scan

<img width="928" alt="Screenshot 2023-05-08 at 11 28 10 AM" src="https://user-images.githubusercontent.com/22426280/236747027-a00d95c2-6566-4b1f-8291-122289b59f84.png">

<img width="872" alt="Screenshot 2023-05-08 at 11 28 54 AM" src="https://user-images.githubusercontent.com/22426280/236747099-535689e5-ee84-4925-8253-d65875a9fbbb.png">

<img width="931" alt="Screenshot 2023-05-08 at 11 29 20 AM" src="https://user-images.githubusercontent.com/22426280/236747236-14d82597-5b31-417a-9439-d63547463754.png">









