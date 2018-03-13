# MongoDB - Getting Started

## Summary

In this guide, I am going to cover the essentials in terms of getting up and running with MongoDB. The best way to start learning new technology is to jump straight in and start using it. Therefore, after a brief introduction to MongoDB, the following sections are all about setting up your own MongoDB instance using 4 different hosting methods. Next, I introduce 5 tools that you can use to work with your MongoDB databases. The next section illustrates methods on creating your own database and collection with data. In this section I also provide a number of examples that illustrate some basic queries to query your data.

**This guide is summarized as follows:**

* [What Is MongoDB](#what-is-mongodb)
* [MongoDB Setup](#mongodb-setup)
  * [Install And Host Locally](#install-and-host-locally)
  * [Install And Host Using Docker](#install-and-host-using-docker)
  * [Host With MongoDB Atlas](#host-with-mongodb-atlas)
  * [Host With MLab](#host-with-mlab)
* [MongoDB Tools](#mongodb-tools)
  * [MongoDB Shell](#mongodb-shell)
  * [MongoDb Compass](#mongodb-compass)
  * [NoSqlBooster](#nosqlbooster)
  * [Robo 3T](#robo-3t)
  * [Visual Studio Code](#visual-studio-code)
* [Basic MongoDB Commands](#basic-mongodb-commands)
* [Conclusion](#conclusion)

Lastly, this guide does not cover the reasons to use MongoDB (perhaps a guide for another time). However, I would like to encourage readers to spend some time researching and understanding the rationale between choosing a NoSql database over a Sql database. MongoDB is not a replacement for SQL databases. It is merely an alternative that may (or may not) align better with ones specific requirements.

The following question posted on Quora may be a good starting point:

[What are some reasons to use traditional RDBMS over NoSQL?](https://www.quora.com/What-are-some-reasons-to-use-traditional-RDBMS-over-NoSQL)

From my personal experience and perspective, I will mention that one should think very carefully before committing to a NoSql database if your data is required to be agnostic. In this case I have found Sql databases to be a far better choice due to the simplicity of modelling data and exposing it in many different ways for many different applications.

---

## What Is MongoDB?

MongoDB is a database. More specifically, it is an open source document-oriented database that has been designed for scalability and simplicity for both developers and sysadmins. Traditional relational database management systems (RDBMS) like MSSQL, Oracle, MySQL, and PostGreSQL store data in tables having a static schema composed of rows and columns. However, MongoDB stores it's data in JSON-like documents having dynamic schemas.

---

## MongoDB Setup

I will discuss 4 options to get up and running with MongoDB:

* Install and host locally
* Install and host in Docker
* Register for and use MongoDB Atlas (Database As A Service)
* Register for and use MLab (Database As A Service)

### Install And Host Locally

Installing MongoDB is relatively straight forward. There are currently 3 platform (Windows, Linux, OSX) releases available and can be found [here](https://www.mongodb.com/download-center?jmp=homepage#community)

For more specific installation instructions, please see the following links:

* [Install MongoDB On Linux](https://docs.mongodb.com/v3.0/administration/install-on-linux)
* [Install MongoDB On Windows](https://docs.mongodb.com/v3.0/tutorial/install-mongodb-on-windows)
* [Install MongoDB On OSX](https://docs.mongodb.com/v3.0/tutorial/install-mongodb-on-os-x)

### Install And Host Using Docker

If you have never used [Docker] before, then this option is less suitable for you as it implies having some Docker knowledge and experience. However, if you are still interested in pursuing this option, then I recommend viewing my [Docker Guide] as a starting point. I also have a [Docker Cheatsheet] that you may find useful.

The Docker image that will be used, is the [official Docker MongoDB image](https://hub.docker.com/_/mongo).

#### Run MongoDB


##### Run MongoDB Using Named Volume

For the following examples I map the Docker MongoDB port of _**27017**_ to a local port of _**37017**_. The reason for this is because I have a local instance on MongoDB running that is already using the _**27017**_ port.

To run a new MongoDB container, execute the following command from the CLI:

```docker
docker run --rm --name mongo-dev -p 37017:27017 -v mongo-dev-db:/data/db -d mongo
```

CLI Command | Description
--- | ---
--rm | remove container when stopped
--name mongo-dev | give container a custom name
-p | map container published port to local port
-v mongo-dev-db/data/db | map the container volume 'data/db' to a custom name 'mongo-dev-db'
-d mongo | run mongo container as a daemon in the background

##### Run MongoDB Using Bind Mount

```bash
cd
mkdir -p mongodb/data/db
docker run --rm --name mongo-dev -p 37017:27017 -v ~/mongodb/data/db:/data/db -d mongo
```

CLI Command | Description
--- | ---
--rm | remove container when stopped
--name mongo-dev | give container a custom name
-p | map container published port to local port
-v ~/mongodb/data/db/data/db | map the container volume 'data/db' to a bind mount '~/mongodb/data/db'
-d mongo | run mongo container as a daemon in the background

#### Access MongoDB

##### Access MongoDB From Local Machine

1. Type the following command to access MongoDB instance hosted within docker container:

   ```docker
   mongo --host localhost --port 37017
   ```

##### Access MongoDB Via Docker Interactive TTY

There are 2 steps to accessing the MongoDB shell.

1. Firstly, access the MongoDB container shell by executing the following command:

   ```bash
   docker exec -it mongo-dev bash
   ```

   This will open an interactive shell (bash) on the MongoDB container.

1. Secondly, once inside the container shell, access the MongoDB shell by executing the following command:

   ```bash
   mongo localhost
   ```

### Host With MongoDB Atlas

[MongoDB Atlas] is a _Data as a Service (DaaS)_ offering, and is hosted in the cloud. There is no installation of MongoDB required and a free tier is available.

To get started, signup for free by registering for a free tier account [here](https://www.mongodb.com/cloud/atlas). The free tier entitles you to 512MB storage.

Please review the [MongoDB Atlas Documentation] for more information.

Once you have registered and setup your MongoDB instance on _Atlas_, you will be presented with a dashboard resembling the following image:

![atlas](https://user-images.githubusercontent.com/33935506/37251882-d19a3818-2520-11e8-97f6-7015435f3cbd.png)

### Host With MLab

[MLab] is a _Data as a Service (DaaS)_ offering, and is hosted in the cloud. There is no installation of MongoDB required and a free tier is available.

To get started, signup for free account [here](https://mlab.com/signup/). The free tier entitles you to 500MB storage.

Please review the [MLab Documentation] for more information.

Once you have registered and setup your MongoDB instance on _MLab_, you will be presented with a dashboard resembling the following image:

![mlab](https://user-images.githubusercontent.com/33935506/37251951-ebb4dd74-2521-11e8-88bc-7db18f0a208c.png)

---

## MongoDB Tools

The tooling for MongoDB has improved a lot of late and there are many options. I have worked with the following tools and I find them all quite good. It's really a matter of personal preference but I find that I tend to use them together.

### MongoDB shell

The MongoDB shell is the default way of interacting with MongoDB databases.

* Connect to MongoDB

  ```bash
  mongo localhost
  ```

* List Available Databases

  ```bash
  show dbs
  ```

* Create Database

  ```bash
  use message_db
  ```

* Create Collection

  ```bash
  db.messages.insert({ message: 'hello mongodb' })
  ```

* List Collections

  ```bash
  show collections
  ```

### MongoDB Compass

[MongoDB Compass] is a Graphical User Interface (GUI) tool that allows one to explore your MongoDB data.

* It has a free addition available
* It is cross platform and is available for Linux, Windows, and Mac
* It supports the following primary features:

  * Run ad hoc queries
  * Perform CRUD operations on data
  * View and optimize query performance

In the following screenshot, I am connected to MongoDB running on my local machine, using MongoDB Compass.

![compass](https://user-images.githubusercontent.com/33935506/37252219-1e877398-2526-11e8-998a-6ecd6bcacaed.png)

For more information on MongoDB Compass, go [here](https://www.mongodb.com/products/compass)

### NoSqlBooster

[NoSqlBooster] is a Graphical User Interface (GUI) that provides an easy to use interface to work with your MongoDB database.

* It has a free addition available
* It is cross platform and is available for Linux, Windows, and Mac
* It provides the following features:

  * Shell extensions
  * Fluent Query API for MongoDB
  * Query MongoDB with SQL
  * Use Node Modules in your script

In the following screenshot, I am connected to MongoDB running on Docker, using NoSqlBooster.

![nosqlbooster](https://user-images.githubusercontent.com/33935506/37252401-18e5b4a6-2529-11e8-940f-9ad49aa82a4a.png)

For more information, please visit the [official NoSqlBooster website](https://nosqlbooster.com/).

### Robo 3T

[Robo 3T] (formerly RoboMongo), is a free GUI tool that can be used to explore your MongoDB databases.

* It is free to use.
* It is cross platform and is available for Linux, Windows, and Mac
* It provides a MongoDB GUI with embedded shell

In the following screenshot, I am connected to a local instance of MongoDB using Robo 3T:

![robo3t](https://user-images.githubusercontent.com/33935506/37252488-3fe78308-252a-11e8-94a7-36c528dca556.png)

[Robo 3T] can be downloaded [here](https://robomongo.org/download)

For more information, go [here](https://robomongo.org/).

### Visual Studio Code

Using the [Azure CosmosDB Extension], one can connect to MongoDB databases in addition to [Azure CosmosDB] databases.

For more information, see the following links:

* [Azure CosmosDB Extension on Market Place](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)
* [Azure CosmosDB Extension on Github](https://github.com/Microsoft/vscode-cosmosdb)

#### Install Azure CosmosDB Extension

* Launch VS Code
* Launch Quick Open (`ctrl+P`) from within VS Code
* Paste the following command and press enter

  ```bash
  ext install ms-vscode.vscode-azureextensionpack
  ```

#### Configure User Settings

Once you have the extension installed, you will need to ensure that you have a path to Mongo configured in your user settings. Press `Ctrl+,` to open your user settings. Therefore, because I am using a Linux based OS, I had to add the following setting to the user settings json:

```json
"mongo.shell.path": "/var/lib/mongodb",
```

#### Connect To Mongo On localhost

To connect to MongoDB instance, follow the following steps:

* Expand _Azure Cosmos DB_ extension panel located in explorer
* Select the option to 'Attach Database Account'
* Select MongoDB
* Enter the connection details to MongoDB instance

  ```shell
  mongodb://localhost:27017
  ```

The steps are shown below:

![vs-code-cosmodb-ext](https://user-images.githubusercontent.com/33935506/37252725-eafbe816-252e-11e8-93ef-155a1e9816bc.gif)

#### Run Commands Using Mongo ScrapBooks

To use the _'ScrapBook Feature'_, follow the following steps:

* Attach to your MongoDB instance as seen above
* Connect to database of your choice
* Select 'New MongoDB ScrapBook' option to open scrapbook editor
* Enter MongoDB command
* Press `Ctrl+Shft+'` to execute

The steps are shown below:

![vs-code-scrapbook](https://user-images.githubusercontent.com/33935506/37253010-8797dee6-2534-11e8-9014-357f6b39c881.gif)

At the time of this writing, there seems to be an [issue with Mongo ScrapBooks](https://github.com/Microsoft/vscode-cosmosdb/issues/214)

---

## Basic MongoDB Commands

Once you have your MongoDB instance running or hosted, open a mongo shell to your MongoDB server. For the purposes of this demonstration, I will be using my local installation 'localhost'.

1. Connect to MongoDB instance

   ```bash
   mongo localhost
   ```

1. Create a 'zips_db' database

   ```bash
   use zips_db
   ```

1. Create 'zips' collection

   ```javascript
   db.zips.insertMany([
     { "city" : "AGAWAM", "loc" : [ -72.622739, 42.070206 ], "pop" : 5338, "state" : "MA", "_id" : "01001" },
     { "city" : "CUSHMAN", "loc" : [ -72.51564999999999, 42.377017 ], "pop" : 36963, "state" : "MA", "_id" : "01002" },
     { "city" : "BARRE", "loc" : [ -72.10835400000001, 42.409698 ], "pop" : 4546, "state" : "MA", "_id" : "01005" },
     { "city" : "BELCHERTOWN", "loc" : [ -72.41095300000001, 42.275103 ], "pop" : 10579, "state" : "MA", "_id" : "01007" },
     { "city" : "BLANDFORD", "loc" : [ -72.936114, 42.182949 ], "pop" : 240, "state" : "MA", "_id" : "01008" },
     { "city" : "BRIMFIELD", "loc" : [ -72.188455, 42.116543 ], "pop" : 706, "state" : "MA", "_id" : "01010" },
     { "city" : "CHESTER", "loc" : [ -72.988761, 42.279421 ], "pop" : 688, "state" : "MA", "_id" : "01011" },
     { "city" : "CHESTERFIELD", "loc" : [ -72.833309, 42.38167 ], "pop"  177, "state" : "MA", "_id" : "01012" },
     { "city" : "CHICOPEE", "loc" : [ -72.607962, 42.162046 ], "pop" : 3396, "state" : "MA", "_id" : "01013" },
     { "city" : "CHICOPEE", "loc" : [ -72.576142, 42.176443 ], "pop" : 1495, "state" : "MA", "_id" : "01020" }
   ])
   ```

1. Search Queries

   * Get total collection count

     ```javascript
     db.zips.count()
     ```

   * Get all zip documents

     ```javascript
     db.zips.find().pretty()
     ```

   * Get 5 zip documents

     ```javascript
     db.zips.find().limit(5).pretty()
     ```

   * Get zip having a city name of 'BLANDFORD'
   
     ```javascript
     db.zips.find({"city": "BLANDFORD"})
     ```

   * Get zip having a city name of 'BLANDFORD', ignore case

     ```javascript
     db.zips.find({"city": { "$regex": /blandford/i } })
     ```

   * Get zip having a city names of 'BLANDFORD' and 'BRIMFIELD'
   
     ```javascript
     db.zips.find({"city": { "$in": ["BLANDFORD", "BRIMFIELD"] } }).pretty()
     ```

   * Get zip having a city names of 'BLANDFORD' and 'BRIMFIELD', ignore case

     ```javascript
     db.zips.find({"city": { "$in": [ /blandford/i, /brimfield/i] } }).pretty ()
     ```

   * Get zip documents having a population greater than or equal to 30000

     ```javascript
     db.zips.find({"pop": { "$gte": 30000}}).pretty()
     ```

   * Get all zip documents and use projection to only display city and population
   
     ```javascript
     db.zips.find({}, { "city": 1, "pop": 1, "_id": 0 })
     ```

   * Get all zip documents and use projection to only display city and population. Sort documents by city name in descending order
   
     ```javascript
     db.zips.find({}, { "city": 1, "pop": 1, "_id": 0 }).sort({"city": -1})
     ```

1. Delete operations

   * Delete all cities called 'CHICOPEE'. Ignore case

     ```javascript
     db.zips.remove({ "city": { "$regex": /chicopee/i }})
     ```

1. Update operations

   * Update the city of 'BLANDFORD' by setting its population to 10

     ```javascript
     db.zips.updateOne({"city": "BLANDFORD"}, { "$set": { "pop": 10}})
     ```

---

## Conclusion

This has been a quick introduction to getting started with MongoDB. In this guide we learned about 4 different ways to host your MongoDB instance. We also learned about 5 different tools that can be used to manage your MongoDB databases. Lastly, we explored some basic queries that can be used to create a database, create a collection, insert data, update data, and fetch data.

MongoDB is an exciting and relatively new database that has a massive community and knowledgebase. I encourage all those that are interested to get involved by trying out the following learning resources:

* [MongoDB Docs] - The official MongoDB documentation
* [MongoDB University] - Free Online Classes on MongoDB from MongoDB

The following MongoDB book is my personal favourite:

* [MongoDB: The Definitive Guide, 2nd Edition] - My personal
* [MongoDB: The Definitive Guide, 3rd Edition (Early Release)]

If you're interested in certification, please see the [MongoDB Professional
Certification Program](https://university.mongodb.com/certification).

---
[MongoDB Docs]: https://docs.mongodb.com
[MongoDB University]: https://university.mongodb.com
[MongoDB: The Definitive Guide, 2nd Edition]: http://shop.oreilly.com/product/0636920028031.do
[MongoDB: The Definitive Guide, 3rd Edition (Early Release)]: http://shop.oreilly.com/product/0636920049531.do
[Docker]: https://www.docker.com
[Docker Guide]:(https://github.com/drminnaar/guides/tree/master/docker-guide)
[Docker Cheatsheet]: https://github.com/drminnaar/cheatsheets/blob/master/docker-cheatsheet.md
[MongoDB Atlas]: https://www.mongodb.com/cloud/atlas
[MongoDB Documentation]: https://docs.atlas.mongodb.com
[Daas]: https://en.wikipedia.org/wiki/Data_as_a_service
[MLab]: https://mlab.com
[MLab Documentation]: https://docs.mlab.com
[MongoDB Compass]: https://www.mongodb.com/products/compass
[NoSqlBooster]: https://nosqlBooster.com
[Robo 3T]: https://robomongo.org
[Azure CosmosDB]: https://azure.microsoft.com/en-us/services/cosmos-db
[Azure CosmosDB Extension]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb