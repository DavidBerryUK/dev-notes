# Mongo
Mongo is a document based database, NoSQL (Not Only SQL) database. Data is stored as collections of documents which as similar to json.

It is very scalable, build in replication and sharding and you do not need to map out data structure before using.

# Downloads

https://www.mongodb.com/developer-tools

* MongoDB Community Server 
* MongoDB Compass - a graphical tool, the quickest way to connect to, and explore your data
* MongoDB Shell - connect, configure and work with your database
* MongoDB for Visual Studio Code plug in

* Download the Community Server

The installation different depending if you are running on windows / mac / unix

# Application - MongoDB Community Server
## Overview

# Application - MongoDB Compass
## Overview

# Application - MongoDB Shell
## Overview



### show the current database
```bash
db
```

### drop the current database
```bash
db.dropDatabase()
```

### Show list of databases
```bash
show db
```

### show a list of collections
```bash
show collections
```

### Switch to or create database
```bash
use testdatabase
```

### Create a new Collection
creates a new collection named cars
```bash
db.createCollections('cars')
```
insert a single row
```bash
db.cars.insert**({
    make: 'ford',
    make: 'focus',
    colour: 'red'
    engine: {
        fuel:'petrol',
        gears:5
    }
})
```
### Insert Multiple Rows
```bash
db.cars.insertMultiple([    
    {
        make: 'ford',
        model: 'focus',
        reg: 'DX54 1PZ',
        colour: 'red'
        engine: {
            fuel:'petrol',
            gears:6
        }   
    },
    {
        make: 'ford',
        model: 'ka',
        reg: 'MV72 2LM',
        colour: 'blue'
        engine: {
            fuel:'petrol',
            gears:5
        }   
    },
    {
        make: 'ford',
        model: 'Puma',
        reg: 'MHV 181',
        colour: 'Red'
        engine: {
            fuel:'petrol',
            gears:6
        }   
    },
])
```
### Listall rows
Lists all items in posts. the output is not very easy to read.
```bash
db.cars.find()
```
Adding the command *pretty* formats the output into a more readable form
```bash
db.cars.find().pretty()
```


### Filtering Rows
The find with the following arguments will return only the red cars
```bash
db.posts.cars.find({colour,"red"})
```
### Sorting Output
sort() will sort results by the car model. *1 = ascending, -1 = descending*
```bash
db.posts.cars.sort({model,1})
``` 
### Counting Rows
Count() will return number of red cars
```bash
db.posts.cars.find({colour,"red"}).count()
```


# Application - MongoDB Visual Studio Tools
## Overview




# References - MongoDB WebSite
https://developer.mongodb.com/article/everything-you-know-is-wrong


# References - YouTube
## Crash Course
https://www.youtube.com/watch?v=-56x56UppqQ