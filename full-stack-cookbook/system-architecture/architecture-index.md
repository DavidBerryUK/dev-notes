//TODO: diagram of architecture over time

# System Architecture

Modern application arcitecture is broken down into multiple layers

* Data Storage
* Backend Applications and Services
* Frontend Applications

## Data Storage
There is an evergrowing number of ways to store data such simple files, relational databases, document databases and many solutions for big data storing huge volumes that can be access quickly.

Traditionally a relational database such as Microsoft SQL Server, Oracle or MySql was the choice of storage for most systems. Overtime document base storage have become more popular, these are also refered to as NoSQL databases. 

MongoDB is a very popular free document databaes.

## Backend Applications
Typically backend applications contain business rules and processes that can be accessed via API's (Application Programming Interface).

The application accepts messages, performs security checks and validation on it before any processing begins.

The process may involve access data from storage, performing calculations and evaluating business rules. After this is complete a response is returned.

Historically they could serve web pages to the user (and still can), but now they perform services, and user interaction is delegated to the frontend application.

Backend applications traditionally were a single monolithic applications, but are now split out a many smaller ones named microservices, each performing a small task on a single business area.


## Frontend Applications
SPA's (Single Page Application) have become the new standard in delivering fron end applications, once very hard to develop are now common place with the development of tools such as React, Vue and Angular.

SPA's are not a single page from the users point of view, but refers to a single page that is delivered by a web server to a users browser.

The page is dynamically updated with different components and pages as the user interacts with the application.

SPA's have the benefit of performing a lot of processing locally, reducing the load from servers, faster response times for the users. SPA's typically allow more complex user interactions.