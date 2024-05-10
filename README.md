# postgresql master slave replication docker-compose file 


## docker-compose.yml

```
version: '3'

services:
  master_postgres:
    image: postgres:latest
    container_name: master_db
    environment:
      POSTGRES_USER: master_user
      POSTGRES_PASSWORD: master_password
      POSTGRES_DB: my_db
    ports:
      - "5432:5432"
    volumes:
      - ./postgres_config/master_postgres.conf:/etc/postgresql/postgresql.conf
      - ./postgres_config/master_pg_hba.conf:/etc/postgresql/pg_hba.conf
    networks:
      - db_network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U master_user"]
      interval: 10s
      timeout: 5s
      retries: 5

  slave_postgres:
    image: postgres:latest
    container_name: slave_db
    environment:
      POSTGRES_USER: slave_user
      POSTGRES_PASSWORD: slave_password
    ports:
      - "5433:5432"
    depends_on:
      - master_postgres
    volumes:
      - ./postgres_config/slave_postgres.conf:/etc/postgresql/postgresql.conf
      - ./postgres_config/slave_pg_hba.conf:/etc/postgresql/pg_hba.conf
    networks:
      - db_network
    command: ["postgres", "-c", "wal_level=replica", "-c", "max_wal_senders=3", "-c", "wal_keep_segments=8"]

networks:
  db_network:
    driver: bridge



```

## new composefile 

```
version: '3'

services:
  db_master:
    image: postgres:latest
    container_name: master_db
    environment:
      POSTGRES_PASSWORD: your_master_password
      POSTGRES_DB: your_database_name
    ports:
      - "5432:5432"
    networks:
      - db_network

  db_slave:
    image: postgres:latest
    container_name: slave_db
    environment:
      POSTGRES_PASSWORD: your_slave_password
      POSTGRES_DB: your_database_name
    depends_on:
      - db_master
    networks:
      - db_network
    command: ["postgres", "-c", "wal_level=logical", "-c", "max_replication_slots=2"]

networks:
  db_network:
    driver: bridge


```
# Check master_slave

 ## 1. Connect to PostgreSQL Server
```
docker exec -it master_db psql -U master_user my_db
```

## Check schema and table 

```
\c my_db
\d test_table
```

## Verfiy Database Existence

```
\l
```
## connect to mydatabase

```
\c my_db

```
## List table

```
#show table
\dt

# chec schema of table
\d test_table

#show the table data
SELECT * FROM test_table;

```

# slave_server

## 1. Connect to PostgreSQL Server
```
docker exec -it slave_db psql -U slave_user

# check subscription setup
SELECT * FROM pg_stat_subscription;

```

## show db
```
\l
```

## Create Subscription for replication 
CREATE SUBSCRIPTION my_subscription
  CONNECTION 'dbname=my_db host=master_db user=slave_user password=slave_password'
  PUBLICATION my_publication;

