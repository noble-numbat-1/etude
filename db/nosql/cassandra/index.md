# Cassandra

## Quickstart

### Common Command

```cql
# Description of table
$ DESCRIBE TABLE store.table;
```

### Using Docker

```bash
# Pull Cassandra image
$ docker pull cassandra:latest

# Create a network
$ docker network create cassandra

# Run cassandra
$ docker run --rm -d --name cassandra --hostname cassandra --network cassandra cassandra
```

Create CQL script: `data.cql`

```cql
CREATE KEYSPACE IF NOT EXISTS store WITH REPLICATION =
{ 'class' : 'SimpleStrategy',
'replication_factor' : '1'
};

CREATE TABLE IF NOT EXISTS store.shopping_cart (
  userid text PRIMARY KEY,
  item_count int,
  last_update_timestamp timestamp
);

INSERT INTO store.shopping_cart
  (userid, item_count, last_update_timestamp)
  VALUES ('9876', 2, toTimeStamp(now()));
INSERT INTO store.shopping_cart
  (userid, item_count, last_update_timestamp)
  VALUES ('1234', 5, toTimeStamp(now()));
```

Using `CQLSH`:

Load data with CQLSH

```bash
# Load data using docker-cqlsh
# data created previously
$ docker run --rm --network cassandra -v "$(pwd)/data.cql:/scripts/data.cql" -e CQLSH_HOST=cassandra -e CQLSH_PORT=9042 -e CQLVERSION=3.4.6 nuvo/docker-cqlsh
```

Interactive CQLSH:

```bash
# Interactive cqlsh using docker
$ docker run --rm -it --network cassandra nuvo/docker-cqlsh cqlsh cassandra 9042 --cqlversion='3.4.5'

$ SELECT * FROM store.shopping_cart;
```

### REF

- DataStax Bulk Loader for Apache Cassandra
