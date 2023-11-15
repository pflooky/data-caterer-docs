---
description: "Guide to create Data Catering jobs. Generate batch and/or event data, validate data, read metadata"

---

# Guides

Below are a list of guides you can follow to create your data generation for your use case.

For any of the paid tier guides, you can use the trial version fo the app to try it out. Details on how to get
the trial can be found [**here**](../../get-started/docker.md#paid-version-trial).

## Scenarios

<div class="grid cards" markdown>

- __[First Data Generation]__ - If you are new, this is the place to start
- __[Multiple Records Per Column Value]__ - How you can generate multiple records per set of columns
- __[Foreign Keys Across Data Sources]__ - Generate matching values across generated data sets
- __[Data Validations]__ - Run data validations after generating data
- __[Auto Generate From Data Connection]__ - Automatically generating data from just defining data sources
- __[Delete Generated Data]__ - Delete the generated data whilst leaving other data
- __[Generate Batch and Event Data]__ - Generate matching batch and event data

</div>

  [First Data Generation]: scenario/first-data-generation.md
  [Multiple Records Per Column Value]: scenario/records-per-column.md
  [Foreign Keys Across Data Sources]: scenario/batch-and-event.md
  [Data Validations]: scenario/data-validation.md
  [Auto Generate From Data Connection]: scenario/auto-generate-connection.md
  [Delete Generated Data]: scenario/delete-generated-data.md
  [Generate Batch and Event Data]: scenario/batch-and-event.md

## Data Sources

<div class="grid cards" markdown>

- __[Files (CSV, JSON, ORC, Parquet)]__ - Generate data for popular file formats
- __[Postgres]__ - JDBC Postgres tables
- __[Cassandra]__ - Cassandra tables
- __[Kafka]__ - Kafka topics
- __[Solace]__ - Solace messages
- __[Marquez]__ - Generate data based on metadata in Marquez
- __[OpenMetadata]__ - Generate data based on metadata in OpenMetadata
- __[HTTP]__ - HTTP requests
- __[Files (Fixed width)]__ - (Soon to document) A variant of CSV but with no separator
- __[MySql]__ - (Soon to document) JDBC MySql tables

</div>

  [Files (CSV, JSON, ORC, Parquet)]: scenario/first-data-generation.md
  [Files (Fixed width)]: scenario/first-data-generation.md
  [Postgres]: scenario/first-data-generation.md
  [Cassandra]: data-source/cassandra.md
  [Kafka]: data-source/kafka.md
  [Solace]: data-source/solace.md
  [Marquez]: data-source/marquez-metadata-source.md
  [OpenMetadata]: data-source/open-metadata-source.md
  [HTTP]: data-source/http.md
  [MySql]: data-source/cassandra.md

## YAML Files

### Base Concept

The execution of the data generator is based on the concept of plans and tasks. A plan represent the set of tasks that
need to be executed,
along with other information that spans across tasks, such as foreign keys between data sources.  
A task represent the component(s) of a data source and its associated metadata so that it understands what the data
should look like
and how many steps (sub data sources) there are (i.e. tables in a database, topics in Kafka). Tasks can define one or
more steps.

### Plan

#### Foreign Keys

[Define foreign keys across data sources in your plan to ensure generated data can match](https://github.com/pflooky/data-caterer-example/blob/main/docker/data/custom/plan/foreign-key-example.yaml)  
[Link to associated task 1](https://github.com/pflooky/data-caterer-example/blob/main/docker/data/custom/task/file/json/json-account-task.yaml)  
[Link to associated task 2](https://github.com/pflooky/data-caterer-example/blob/main/docker/data/custom/task/jdbc/postgres/postgres-account-task.yaml)

### Task

| Data Source Type | Data Source | Sample Task                                                                                                                            | Notes                                                             |
|------------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------|
| Database         | Postgres    | [Sample](https://github.com/pflooky/data-caterer-example/blob/main/docker/data/custom/task/jdbc/postgres/postgres-account-task.yaml)   |                                                                   |
| Database         | MySQL       | [Sample](https://github.com/pflooky/data-caterer-example/blob/main/docker/data/custom/task/jdbc/mysql/mysql-account-task.yaml)         |                                                                   |
| Database         | Cassandra   | [Sample](https://github.com/pflooky/data-caterer-example/blob/main/docker/data/custom/task/cassandra/cassandra-customer-task.yaml)     |                                                                   |
| File             | CSV         | [Sample](https://github.com/pflooky/data-caterer-example/blob/main/docker/data/custom/task/file/csv/csv-transaction-task.yaml)         |                                                                   |
| File             | JSON        | [Sample](https://github.com/pflooky/data-caterer-example/blob/main/docker/data/custom/task/file/json/json-account-task.yaml)           | Contains nested schemas and use of SQL for generated values       |
| File             | Parquet     | [Sample](https://github.com/pflooky/data-caterer-example/blob/main/docker/data/custom/task/file/parquet/parquet-transaction-task.yaml) | Partition by year column                                          |
| Kafka            | Kafka       | [Sample](https://github.com/pflooky/data-caterer-example/blob/main/docker/data/custom/task/kafka/kafka-account-task.yaml)              | Specific base schema to be used, define headers, key, value, etc. |
| JMS              | Solace      | [Sample](https://github.com/pflooky/data-caterer-example/blob/main/docker/data/custom/task/jms/solace/jms-account-task.yaml)           | JSON formatted message                                            |
| HTTP             | PUT         | [Sample](https://github.com/pflooky/data-caterer-example/blob/main/docker/data/custom/task/http/http-account-task.yaml)                | JSON formatted PUT body                                           |

### Configuration

[Basic configuration](https://github.com/pflooky/data-caterer-example/blob/main/docker/data/custom/application.conf)

## Docker-compose

To see how it runs against different data sources, you can run using `docker-compose` and set `DATA_SOURCE` like below

```shell
./gradlew build
cd docker
DATA_SOURCE=postgres docker-compose up -d datacaterer
```

Can set it to one of the following:

- postgres
- mysql
- cassandra
- solace
- kafka
- http
