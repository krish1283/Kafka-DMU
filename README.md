# Data migration Utility with kafka 
Data migration with kafka connect, debzium, postgreSQL source and Redis sink

This is a working POC for migrating data and streaming changes from PostgreSQL DB to Redis cache using open source kafka connect and Debzium CDC.

# Pre-requisite
1. You have a apache kafka available (K-Raft v>=3.3.1) in standalone or cluster mode and all dependencies of kafka are present on machine.
2. You have a redis cluster(standalone will also work though) master/slave(3/3 preferably) running on local.

# How To use it on local
1. Run below command to generate UUID for kafka storage, this is required before any data is stored on kafka topic in RAFT cluster
      /kafka/kafka331$ ./bin/kafka-storage.sh random-uuid -- output Example:- 8ddle9dWSk2pnq2g959s0Q (this has to be used in next command)
2. Creating cluster and log directories by running below command
    ./bin/kafka-storage.sh format -t 8ddle9dWSk2pnq2g959s0Q -c ./config/kraft/server.properties

3. run kafka server
    ./bin/kafka-server-start.sh ./config/kraft/server.properties

4. Follow instrction in the mentioned link to install postgreSQL
      https://www.cherryservers.com/blog/how-to-install-and-setup-postgresql-server-on-ubuntu-20-04

5. #check postgres status
      service postgresql status

6. start postgres service
      service postgresql start
Note:- Defaust admin user and password is postgres and postgres

7.command to interact with local postgreSQL database engine
        sudo -u postgres psql

8. check the list of databases
        \l
        
9. Create and Populate a New Database and connect to it
        CREATE DATABASE mytestdb;
        \c mytestdb
        

9. Create and Populate a New Database and connect to it
      CREATE DATABASE mytestdb;
        \c mytestdb

10. Create table and add some data
        CREATE TABLE testTable (id SERIAL PRIMARY KEY, first_name VARCHAR, last_name VARCHAR, role VARCHAR);
        \dt
        INSERT INTO testTable (first_name, last_name, role) VALUES ('John', 'Smith', 'CEO');
        SELECT * FROM testTable;
 11. Downlaod 3 properties files from this repo and put them under kafka/config folder of your kafka installation.
 
 12. Downlaod the zip from this repo, unzip it as debezium-connector-postgres folder.
 
 13. Before running kafka connect set below to the folder created in step 12
          export CLASSPATH=/.../..../.../debezium-connector-postgres/*
          
 14. Change wal level in postgreSQL DB for debzium and restart postgresql
          ALTER SYSTEM SET wal_level=logical;
          
 15. Open console in Kafka installaion folder and run below command
          ./bin/connect-standalone.sh ./config/connect-standalone.properties ./config/connect-pgdebzium-source.properties ./config/connect-redis-sink.properties
          
 16. Verify logs, there should be no error reported.
 
 17. Check Redis DB, You can use open source RESP.app to have a GUI connecting to redis cluster. You should be able to view keys like 1.2.3.4... and values(in json form).
 
 18. Now update or delete a record in PsotgreSQL and check data in redis. It should be updated/deleted immediately.
 
 # Any comments, suggestion for improvement are welcome !!!
