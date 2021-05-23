# Using MongoDB with Docker

Small document to show how to get a docker instance of MongoDB up and running. This initial basics chapter
will create a docker instance with a mongo db running.

##  BASICS - Downloading the docker image
First ensure docker is running then pull the latest mongo image

```
docker pull mongo:latest
```

##  BASICS - Starting , Stopping and Removing the MongoDB Container
Start a mongo service inside a docker instance

```
docker run --name cont-orders-database -d mongo:latest
```

Stopping the container
```
docker stop cont-orders-database
```

Removing the container
```
docker rm cont-orders-database
```

## BASICS - Enter the shell inside the docker
```
docker exec -it cont-orders-database bash
```

### Show existing databases
```
show dbs

admin   0.000GB
config  0.000GB
local   0.000GB
```

### Create new Database or Switch to existing Database
Note that a database will not be listed with the **show dbs** command until it contains at least 1 document
```
use Orders
```

### Show existing database in use
```
db

orders  
```

### insert document
```
db.orders.insert({"orderId":"123456","name":"new car"})
```

### list documents
List all documents with no formatting formatting
```
db.orders.find()

{ "_id" : ObjectId("60aabb97e0dc3abc983de1a7"), "orderId" : "123456", "name" : "new car" }
```

List all documents formatted as json object
```
db.orders.find().pretty()

{
        "_id" : ObjectId("60aabb97e0dc3abc983de1a7"),
        "orderId" : "123456",
        "name" : "new car"
}
```
