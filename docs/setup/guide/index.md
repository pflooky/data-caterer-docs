# Guides

Below are a list of guides you can follow to create your data generation for your use case.
  
Checkout this [repo](https://github.com/pflooky/data-caterer-example) for example Java and Scala API usage.

## Java/Scala API

### Scenarios

!!! note "Free tier scenarios"

    - __[First Data Generation]__ - If you are new, this is the place to start
    - __[Multiple Records Per Column Value]__ - How you can generate multiple records per set of columns
    - __[Foreign Keys Across Data Sources]__ - (Soon to document) Generate matching values across generated data sets
    - __[Data Validations]__ - (Soon to document) Run data validations after generating data

      [First Data Generation]: scenario/first-data-generation.md
      [Multiple Records Per Column Value]: scenario/records-per-column.md
      [Foreign Keys Across Data Sources]: scenario/first-data-generation.md
      [Data Validations]: scenario/first-data-generation.md

!!! example "Paid tier scenarios"

    - __[Auto Generate From Data Connection]__ - Automatically generating data from just defining data sources
    - __[Delete Generated Data]__ - Delete the generated data whilst leaving other data
    - __[Generate Batch and Event Data]__ - Generate matching batch and event data

      [Auto Generate From Data Connection]: scenario/auto-generate-connection.md
      [Delete Generated Data]: scenario/delete-generated-data.md
      [Generate Batch and Event Data]: scenario/batch-and-event.md
  
### Data Sources

!!! note "Free tier data sources"

    <div class="grid cards" markdown>

    - __[Files (CSV, JSON, ORC, Parquet)]__ - Generate data for files with separator (comma separated by default)
    - __[Files (Fixed width)]__ - A variant of CSV but with no separator
    - __[Postgres]__ - JDBC Postgres tables

    </div>

      [Files (CSV, JSON, ORC, Parquet)]: scenario/first-data-generation.md
      [Files (Fixed width)]: scenario/first-data-generation.md
      [Postgres]: scenario/first-data-generation.md

!!! example "Paid tier data sources"

    <div class="grid cards" markdown>

    - __[Cassandra]__ - Cassandra tables
    - __[Kafka]__ - Kafka topics
    - __[MySql]__ - (Soon to document) JDBC MySql tables
    - __[Solace]__ - (Soon to document) Solace messages
    - __[HTTP]__ - (Soon to document) HTTP requests

    </div>

      [Cassandra]: data-source/cassandra.md
      [Kafka]: data-source/kafka.md
      [MySql]: data-source/cassandra.md
      [Solace]: data-source/cassandra.md
      [Http]: data-source/cassandra.md

## YAML Files

### Base Concept

The execution of the data generator is based on the concept of plans and tasks. A plan represent the set of tasks that need to be executed, 
along with other information that spans across tasks, such as foreign keys between data sources.  
A task represent the component(s) of a data source and its associated metadata so that it understands what the data should look like 
and how many steps (sub data sources) there are (i.e. tables in a database, topics in Kafka). Tasks can define one or more steps.

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
