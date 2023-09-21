# Data Source Connections

Details of all the connection configuration supported can be found in the below subsections for each type of connection.
  
These configurations can be done via API or from configuration. Examples of both are shown for each data source below.

## Supported Data Connections

| Data Source Type | Data Source                                         |
|------------------|-----------------------------------------------------|
| Database         | Postgres, MySQL, Cassandra                          |
| File             | CSV, JSON, ORC, Parquet                             |
| Kafka            | Kafka                                               |
| JMS              | Solace                                              |
| HTTP             | GET, PUT, POST, DELETE, PATCH, HEAD, TRACE, OPTIONS |

### API

All connection details require a name. Depending on the data source, you can define additional options which may be used
by the driver or connector for connecting to the data source.

### Configuration file

All connection details follow the same pattern.

```
<connection format> {
    <connection name> {
        <key> = <value>
    }
}
```

!!! info "Overriding configuration"
    When defining a configuration value that can be defined by a system property or environment variable at runtime, you can
    define that via the following:
    
    ```
    url = "localhost"
    url = ${?POSTGRES_URL}
    ```
    
    The above defines that if there is a system property or environment variable named `POSTGRES_URL`, then that value will
    be used for the `url`, otherwise, it will default to `localhost`.

## Data sources

To find examples of a task for each type of data source, please check out [this page](../../guide/index.md).

### File

Linked [here](https://spark.apache.org/docs/latest/sql-data-sources-generic-options.html) is a list of generic options
that can be included as part of your file data source configuration if required. Links to specific file type
configurations can be found below.

#### CSV

=== "Java"

    ```java
    csv("customer_transactions", "/data/customer/transaction")
    ```

=== "Scala"

    ```scala
    csv("customer_transactions", "/data/customer/transaction")
    ```

=== "application.conf"

    ```
    csv {
      customer_transactions {
        path = "/data/customer/transaction"
        path = ${?CSV_PATH}
      }
    }
    ```

[Other available configuration for CSV can be found here](https://spark.apache.org/docs/latest/sql-data-sources-csv.html#data-source-option)

#### JSON

=== "Java"

    ```java
    json("customer_transactions", "/data/customer/transaction")
    ```

=== "Scala"

    ```scala
    json("customer_transactions", "/data/customer/transaction")
    ```

=== "application.conf"

    ```
    json {
      customer_transactions {
        path = "/data/customer/transaction"
        path = ${?JSON_PATH}
      }
    }
    ```

[Other available configuration for JSON can be found here](https://spark.apache.org/docs/latest/sql-data-sources-json.html#data-source-option)

#### ORC

=== "Java"

    ```java
    orc("customer_transactions", "/data/customer/transaction")
    ```

=== "Scala"

    ```scala
    orc("customer_transactions", "/data/customer/transaction")
    ```

=== "application.conf"

    ```
    orc {
      customer_transactions {
        path = "/data/customer/transaction"
        path = ${?ORC_PATH}
      }
    }
    ```

[Other available configuration for ORC can be found here](https://spark.apache.org/docs/latest/sql-data-sources-orc.html#configuration)

#### Parquet

=== "Java"

    ```java
    parquet("customer_transactions", "/data/customer/transaction")
    ```

=== "Scala"

    ```scala
    parquet("customer_transactions", "/data/customer/transaction")
    ```

=== "application.conf"
    
    ```
    parquet {
      customer_transactions {
        path = "/data/customer/transaction"
        path = ${?PARQUET_PATH}
      }
    }
    ```

[Other available configuration for Parquet can be found here](https://spark.apache.org/docs/latest/sql-data-sources-parquet.html#data-source-option)

#### Delta (not supported yet)

=== "Java"

    ```java
    delta("customer_transactions", "/data/customer/transaction")
    ```

=== "Scala"

    ```scala
    delta("customer_transactions", "/data/customer/transaction")
    ```

=== "application.conf"

    ```
    delta {
      customer_transactions {
        path = "/data/customer/transaction"
        path = ${?DELTA_PATH}
      }
    }
    ```

### JDBC

Follows the same configuration used by Spark as
found [here](https://spark.apache.org/docs/latest/sql-data-sources-jdbc.html#data-source-option).  
Sample can be found below

=== "Java"

    ```java
    postgres(
        "customer_postgres",                            #name
        "jdbc:postgresql://localhost:5432/customer",    #url
        "postgres",                                     #username
        "postgres"                                      #password
    )
    ```

=== "Scala"

    ```scala
    postgres(
        "customer_postgres",                            #name
        "jdbc:postgresql://localhost:5432/customer",    #url
        "postgres",                                     #username
        "postgres"                                      #password
    )
    ```

=== "application.conf"

    ```
    jdbc {
        customer_postgres {
            url = "jdbc:postgresql://localhost:5432/customer"
            url = ${?POSTGRES_URL}
            user = "postgres"
            user = ${?POSTGRES_USERNAME}
            password = "postgres"
            password = ${?POSTGRES_PASSWORD}
            driver = "org.postgresql.Driver"
        }
    }
    ```

Ensure that the user has write permission, so it is able to save the table to the target tables.

??? tip "SQL Permission Statements"

    ```sql
    GRANT INSERT ON <schema>.<table> TO <user>;
    ```

#### Postgres

Can see example API or Config definition for Postgres connection above.

##### Permissions

Following permissions are required when generating plan and tasks:

??? tip "SQL Permission Statements"

    ```sql
    GRANT SELECT ON information_schema.tables TO < user >;
    GRANT SELECT ON information_schema.columns TO < user >;
    GRANT SELECT ON information_schema.key_column_usage TO < user >;
    GRANT SELECT ON information_schema.table_constraints TO < user >;
    GRANT SELECT ON information_schema.constraint_column_usage TO < user >;
    ```

#### MySQL

=== "Java"

    ```java
    mysql(
        "customer_mysql",                       #name
        "jdbc:mysql://localhost:3306/customer", #url
        "root",                                 #username
        "root"                                  #password
    )
    ```

=== "Scala"

    ```scala
    mysql(
        "customer_mysql",                       #name
        "jdbc:mysql://localhost:3306/customer", #url
        "root",                                 #username
        "root"                                  #password
    )
    ```

=== "application.conf"

    ```
    jdbc {
        customer_mysql {
            url = "jdbc:mysql://localhost:3306/customer"
            user = "root"
            password = "root"
            driver = "com.mysql.cj.jdbc.Driver"
        }
    }
    ```

##### Permissions

Following permissions are required when generating plan and tasks:

??? tip "SQL Permission Statements"

    ```sql
    GRANT SELECT ON information_schema.columns TO < user >;
    GRANT SELECT ON information_schema.statistics TO < user >;
    GRANT SELECT ON information_schema.key_column_usage TO < user >;
    ```

### Cassandra

Follows same configuration as defined by the Spark Cassandra Connector as
found [here](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md)

=== "Java"

    ```java
    cassandra(
        "customer_cassandra",   #name
        "localhost:9042",       #url
        "cassandra",            #username
        "cassandra",            #password
        Map.of()                #optional additional connection options
    )
    ```

=== "Scala"

    ```scala
    cassandra(
        "customer_cassandra",   #name
        "localhost:9042",       #url
        "cassandra",            #username
        "cassandra",            #password
        Map()                #optional additional connection options
    )
    ```

=== "application.conf"

    ```
    org.apache.spark.sql.cassandra {
        customer_cassandra {
            spark.cassandra.connection.host = "localhost"
            spark.cassandra.connection.host = ${?CASSANDRA_HOST}
            spark.cassandra.connection.port = "9042"
            spark.cassandra.connection.port = ${?CASSANDRA_PORT}
            spark.cassandra.auth.username = "cassandra"
            spark.cassandra.auth.username = ${?CASSANDRA_USERNAME}
            spark.cassandra.auth.password = "cassandra"
            spark.cassandra.auth.password = ${?CASSANDRA_PASSWORD}
        }
    }
    ```

##### Permissions

Ensure that the user has write permission, so it is able to save the table to the target tables.

??? tip "CQL Permission Statements"

    ```sql
    GRANT INSERT ON <schema>.<table> TO <user>;
    ```

Following permissions are required when enabling `configuration.enableGeneratePlanAndTasks(true)` as it will gather
metadata information about tables and columns from the below tables.

??? tip "CQL Permission Statements"

    ```sql
    GRANT SELECT ON system_schema.tables TO <user>;
    GRANT SELECT ON system_schema.columns TO <user>;
    ```

### Kafka

Define your Kafka bootstrap server to connect and send generated data to corresponding topics. Topic gets set at a step
level.  
Further details can be
found [here](https://spark.apache.org/docs/latest/structured-streaming-kafka-integration.html#writing-data-to-kafka)

=== "Java"

    ```java
    kafka(
        "customer_kafka",   #name
        "localhost:9092"    #url
    )
    ```

=== "Scala"

    ```scala
    kafka(
        "customer_kafka",   #name
        "localhost:9092"    #url
    )
    ```

=== "application.conf"

    ```
    kafka {
        customer_kafka {
            kafka.bootstrap.servers = "localhost:9092"
            kafka.bootstrap.servers = ${?KAFKA_BOOTSTRAP_SERVERS}
        }
    }
    ```

When defining your schema for pushing data to Kafka, it follows a specific top level schema.  
An example can be found [here](https://github.com/pflooky/data-caterer-example/blob/main/docker/data/custom/task/kafka/kafka-account-task.yaml).  
You can define the key, value, headers, partition or topic by following the linked schema.

### JMS

Uses JNDI lookup to send messages to JMS queue. Ensure that the messaging system you are using has your queue/topic
registered
via JNDI otherwise a connection cannot be created.

=== "Java"

    ```java
    solace(
        "customer_solace",                                      #name
        "smf://localhost:55554",                                #url
        "admin",                                                #username
        "admin",                                                #password
        "default",                                              #vpn name
        "/jms/cf/default",                                      #connection factory
        "com.solacesystems.jndi.SolJNDIInitialContextFactory"   #initial context factory
    )
    ```

=== "Scala"

    ```scala
    solace(
        "customer_solace",                                      #name
        "smf://localhost:55554",                                #url
        "admin",                                                #username
        "admin",                                                #password
        "default",                                              #vpn name
        "/jms/cf/default",                                      #connection factory
        "com.solacesystems.jndi.SolJNDIInitialContextFactory"   #initial context factory
    )
    ```

=== "application.conf"

    ```
    jms {
        customer_solace {
            initialContextFactory = "com.solacesystems.jndi.SolJNDIInitialContextFactory"
            connectionFactory = "/jms/cf/default"
            url = "smf://localhost:55555"
            url = ${?SOLACE_URL}
            user = "admin"
            user = ${?SOLACE_USER}
            password = "admin"
            password = ${?SOLACE_PASSWORD}
            vpnName = "default"
            vpnName = ${?SOLACE_VPN}
        }
    }
    ```

### HTTP

Define any username and/or password needed for the HTTP requests.  
The url is defined in the tasks to allow for generated data to be populated in the url.

=== "Java"

    ```java
    http(
        "customer_api", #name
        "admin",        #username
        "admin"         #password
    )
    ```

=== "Scala"

    ```scala
    http(
        "customer_api", #name
        "admin",        #username
        "admin"         #password
    )
    ```

=== "application.conf"

    ```
    http {
        customer_api {
            user = "admin"
            user = ${?HTTP_USER}
            password = "admin"
            password = ${?HTTP_PASSWORD}
        }
    }
    ```
