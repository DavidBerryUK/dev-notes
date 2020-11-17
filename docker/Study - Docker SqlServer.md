# Docker with SQL Server

## Tools
* Install Docker Desktop
* Install Visual Studio Code
* Install NotdeJS

## Install for Windows
https://docs.docker.com/docker-for-windows/install/


## Introduction
Docker is a lightweight virtual machine.

## Containers Available for Sql Server
https://hub.docker.com/_/microsoft-mssql-server


# Steps
## Create new Directory



## Open up in Visual Studio Code
### Install packages for application


### create **docker-compose.yml** file
```bash
version: "3.7"
services:
    sql-server-db:
        container_name:sql-server-db
        image:"mcr.microsoft.com/mssql/server:2019-latest"
        ports:
            - "1433:1433"
        environment:
            SA_PASSWORD: "super_duper_password"
            ACCEPT_EULA: "Y"
```

???see what dockers are running???
```bash
docker-compose ps
``` 

**-d** daemon - run in the background
```bash
docker-compose up -d
```


# Setup TestDB database

```bash
docker exec -it sql-server-db "bash"
```

```bash
ls
/opt/mssql-tools/bin/sqlcmd -S localhost -U SA -p super_duper_password
## view databases
select name from sys.databases
go

## create database
create database testdb
go

## CREATE TEST DATABASE
use testdb
create table users (id Int, name NVARCHAR(50), email NVARCHAR(255));
go

## INSERT ROW INTO USERS TABLE
insert into users users (1,'Bill Gates','bill.gates@microsoft.comn')
go

## Verify the row exists
select * from users
go

CTRL+C - to exit CMDSQL
CTRL+C - to exit Bash
```

# Create simple application to access the  database

```bash
npm install --save mssql
```


### create **index.js** file
```javascript
const sql = require('mssql');

const config = {
    user: 'sa',
    password: 'super_duper_password',
    server: 'localhost',
    database: 'testdb',
    options: {
        enableArithAbort: true
    }
}

const run = async() => {
    let pool;
    try {
        console.log('Connection Opening...');
        pool = await sql.connect(config);
        const { recordset } = await sql.query`select * from users;`;
        
        console.log(recordset);

    } catch (err) {
        console.log(err)
    } finally {
        await pool.close();
        console.log('Connection Closed');
    }
}

run();

```

## Run The Application
The node application will connect to the SQL inside the Docker Container
```bash
node index.js
```




# Docker Compose Common Commands

## See whats running
```bash
docker-compose ps
```
## run a docker image
Launches current orchestration defined in the docker-compose.yml file

**-d** - daemon - run in the background
```bash
```bash
docker-compose up -d
```
## take down an image
Takes down the current orchestration defined in the docker-compose.yml file
```bash
docker-compose down 
```

## show docker logs
Show the logs for a named docker container. If a docker fails to launch this s a good place investigate any issues.
```bash
docker-compose logs {sql-server-db}
```

# References
## How to run SQL Server in a Docker Container
https://www.youtube.com/watch?v=RAE-VcZ3u2A