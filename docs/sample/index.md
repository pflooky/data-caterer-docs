# Samples

Below are examples of different types of plans and tasks that can be helpful when trying to create your own. You can use
these as a template or to search for something related to your particular use case.

## Plan

### Foreign Keys

[Define foreign keys across data sources in your plan to ensure generated data can match](plan/foreign-key-example-plan.yaml)  
[Link to associated task 1](task/file/json/json-account-task.yaml)  
[Link to associated task 2](task/jdbc/postgres/postgres-account-task.yaml)

## Task

| Data Source Type | Data Source | Sample Task                                               | Notes                                                       |
|------------------|-------------|-----------------------------------------------------------|-------------------------------------------------------------|
| Database         | Postgres    | [Sample](task/jdbc/postgres/postgres-account-task.yaml)   |                                                             |
| Database         | Cassandra   | [Sample](task/cassandra/cassandra-customer-task.yaml)     |                                                             |
| File             | CSV         | [Sample](task/file/csv/csv-transaction-task.yaml)         |                                                             |
| File             | JSON        | [Sample](task/file/json/json-account-task.yaml)           | Contains nested schemas and use of SQL for generated values |
| File             | Parquet     | [Sample](task/file/parquet/parquet-transaction-task.yaml) |                                                             |
| HTTP             | PUT         | [Sample](task/http/http-account-task.yaml)                | JSON formatted PUT body                                     |
| JMS              | Solace      | [Sample](task/jms/solace/jms-account-task.yaml)           | JSON formatted message                                      |


## Configuration

[Basic configuration](conf/application.conf)
