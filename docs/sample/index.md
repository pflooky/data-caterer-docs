# Samples

Below are examples of different types of plans and tasks that can be helpful when trying to create your own. You can use
these as a template or to search for something related to your particular use case.
  
Checkout this [repo](https://github.com/pflooky/data-caterer-example) for example Java and Scala API usage.

## Base Concept

The execution of the data generator is based on the concept of plans and tasks. A plan represent the set of tasks that need to be executed, 
along with other information that spans across tasks, such as foreign keys between data sources.  
A task represent the component(s) of a data source and its associated metadata so that it understands what the data should look like 
and how many steps (sub data sources) there are (i.e. tables in a database, topics in Kafka). Tasks can define one or more steps.

## Plan

### Foreign Keys

[Define foreign keys across data sources in your plan to ensure generated data can match](docker/data/custom/plan/foreign-key-example.yaml)  
[Link to associated task 1](docker/data/custom/task/file/json/json-account-task.yaml)  
[Link to associated task 2](docker/data/custom/task/jdbc/postgres/postgres-account-task.yaml)

## Task

| Data Source Type | Data Source | Sample Task                                                                  | Notes                                                             |
|------------------|-------------|------------------------------------------------------------------------------|-------------------------------------------------------------------|
| Database         | Postgres    | [Sample](docker/data/custom/task/jdbc/postgres/postgres-account-task.yaml)   |                                                                   |
| Database         | MySQL       | [Sample](docker/data/custom/task/jdbc/mysql/mysql-account-task.yaml)         |                                                                   |
| Database         | Cassandra   | [Sample](docker/data/custom/task/cassandra/cassandra-customer-task.yaml)     |                                                                   |
| File             | CSV         | [Sample](docker/data/custom/task/file/csv/csv-transaction-task.yaml)         |                                                                   |
| File             | JSON        | [Sample](docker/data/custom/task/file/json/json-account-task.yaml)           | Contains nested schemas and use of SQL for generated values       |
| File             | Parquet     | [Sample](docker/data/custom/task/file/parquet/parquet-transaction-task.yaml) | Partition by year column                                          |
| Kafka            | Kafka       | [Sample](docker/data/custom/task/kafka/kafka-account-task.yaml)              | Specific base schema to be used, define headers, key, value, etc. |
| JMS              | Solace      | [Sample](docker/data/custom/task/jms/solace/jms-account-task.yaml)           | JSON formatted message                                            |
| HTTP             | PUT         | [Sample](docker/data/custom/task/http/http-account-task.yaml)                | JSON formatted PUT body                                           |


## Configuration

[Basic configuration](docker/data/custom/application.conf)
