# Quick Note

## Cassandra Summary

### Cassandra Technique

- Replication Strategy: determine which nodes act as replicas for a token range.
  - Each keyspace has its own strategy.
  - SimpleStrategy:
  - NetworkTopologyStrategy:
    - recommended for production
    - requires a specified replication factor for each datacenter in the cluster.
    - easier to add new physical or virtual datacenters to the cluster later
- Data versioning
  - Cassandra uses mutation **timestamp** versioning.
  - provided either from a client clock or the coordinator node’s clock.
  - each column in a row resolves concurrent mutations according to last-write-wins conflict resolution.
  - data in each replica will eventually consistency.
- Dataset partitioning by hashing
  - Each partition is replicated to multiple physical nodes
  - Every replica independently accept mutations, and each mutation is timestamped.
  - Replica with the latest timestamped win.
  - In case has the same timestamped, the "delete" win, then the update with lexically larger value win.

Data Modeling

- The data access patterns and queries are used to design the database tables.
- Denormalize data:
  - All entities involved in a query should be in the same table.
  - A single entity may be included in multiple tables, data is duplicated
  - No join between tables.
- Query should keep a minimum the number of partitions read as data store across diff node.
- A partition key is generated from the first field of a primary key.

### DropWizard

Project organization and structure:

- api: Representations. Request and response bodies.
- cli: Commands
- client: Client code that accesses external HTTP services.
- core: Domain implementation; where objects not used in the API such as POJOs, validations, crypto, etc, reside.
- db: Database access classes
- health: Health Checks
- resources: Resources
- MyApplication: The application class
- MyApplicationConfiguration: configuration class

Class and its definition:

- Main entry point: Application subclass
  - Environment: contains resources, servlets, jersey provider, etc. which can be used to register resource instances.
  - Has access to configuration instance.
- Configuration class
  - will be loaded by application and value is populated from the provieded yaml file, java properties or env.
  - factory class can be used to build and provided config
- Representation class:
  - used as dto
  - dropwizard support JSON and other format to map to/from POJO.
- Resource class: is a controller which register to jersey
- Managed Objects (impl Managed): is used to tie the object's lifecycle to application lifecycle.
- Bundles (impl ConfiguredBundle): is used to provide reusable group of functionality.
- Commands: is used to run on command line.
- Tasks: is used to provide run-time action on administrative port.

When the application start, it spin up Jetty server with two handlers, one for application and the other for admin. The application startup sequence:

- Application#run(args): the run method will be the first to be called.
- application get bootstrap: application temp environment
- Application#initialize(bootstrap): used to initialize our application, we can add more functionality to above bootstrap
- bootstrap register metrics
- Create CLI class and call the "run" method which
  - parse commandline args and load configuration file
  - create Enviroment: is an object similar to bootstrap object above.
  - Bootstrap bundles (FIFO order)
  - Call application#run(config, evn): our impl
  - Start Jetty server
