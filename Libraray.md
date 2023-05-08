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

