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
