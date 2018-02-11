# Run MongoDB

In this example, I run a MongoDB container using named volumes and bind mounts.

Procedure:
- [Run MongoDB](#run-mongodb)
    - [Run MongoDB Using Named Volume](#run-mongodb-using-named-volume)
    - [Run MongoDB Using Bind Mount](#run-mongodb-using-bind-mount)
    - [Access MongoDB](#access-mongodb)
        - [Connect to MongoDb](#connect-to-mongodb)
        - [Show Databases](#show-databases)
        - [Create New Database](#create-new-database)

## Run MongoDB Using Named Volume

To run a new MongoDB container, execute the following command from the CLI:

```docker
docker run --rm --name mongo-dev -v mongo-dev-db:/data/db -d mongo
```

CLI Command | Description
--- | ---
--rm | remove container when stopped
--name mongo-dev | give container a custom name
-v mongo-dev-db/data/db | map the container volume 'data/db' to a custom name 'mongo-dev-db'
-d mongo | run mongo container as a daemon in the background

## Run MongoDB Using Bind Mount

```bash
cd
mkdir -p mongodb/data/db
docker run --rm --name mongo-dev -v ~/mongodb/data/db:/data/db -d mongo
```

CLI Command | Description
--- | ---
--rm | remove container when stopped
--name mongo-dev | give container a custom name
-v ~/mongodb/data/db/data/db | map the container volume 'data/db' to a bind mount '~/mongodb/data/db'
-d mongo | run mongo container as a daemon in the background

## Access MongoDB

### Connect to MongoDb

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

### Show Databases

Once connected to MongoDB shell, run the following command to show a list of databases:

```bash
show dbs
```

### Create New Database

Create a new database and collection

```javascript
use test
db.messages.insert({"message": "Hello World!"})
db.messages.find()
```
