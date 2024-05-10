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
## psql command 
```
\l  -- List all databases
\c my_db  -- Connect to the my_db database
\dt  -- List all tables in the current database (my_db)
\d my_table  -- Describe the structure of the my_table

```

# slave_server

## 1. Connect to PostgreSQL Server
```
docker exec -it slave_db psql -U slave_user

# check subscription setup
SELECT * FROM pg_stat_subscription;
```

# Master Server
--------------------------------------------------------------
## Insert Dummy data on master_slave

```
# connect postgresql data

docker exec -it master_db psql -U master_user -d my_db

# create table
CREATE TABLE my_table (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255),
    email VARCHAR(255),
    age INT
);

# insert data
INSERT INTO my_table (name, email, age) VALUES
    ('John Doe', 'john@example.com', 30),
    ('Jane Smith', 'jane@example.com', 25),
    ('Alice Johnson', 'alice@example.com', 35);

# Verify data
SELECT * FROM my_table;

```

