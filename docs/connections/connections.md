# Data Source Connections

Details of all the connection configuration supported can be found in the below subsections for each type of connection.

## Supported Data Connections

| Data Source Type | Data Source                    |
|------------------|--------------------------------|
| Database         | Postgres, MySQL, Cassandra     |
| File             | CSV, JSON, ORC, Parquet, Delta |
| JMS              | Solace                         |
| HTTP             | GET, PUT, POST                 |

All connection details follow the same pattern.

```
<connection format> {
    <connection name> {
        <key> = <value>
    }
}
```

When defining a configuration value that can be defined by a system property or environment variable at runtime, you can
define that via the following:

```
url = "localhost"
url = ${?POSTGRES_URL}
```

The above defines that if there is a system property or environment variable named `POSTGRES_URL`, then that value will
be used for the `url`, otherwise, it will default to `localhost`.

### File

Linked [here](https://spark.apache.org/docs/latest/sql-data-sources-generic-options.html) is a list of generic options
that can be included as part of your file data source configuration if required. Links to specific file type
configurations can be found below.

#### CSV

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

```
parquet {
  customer_transactions {
    path = "/data/customer/transaction"
    path = ${?PARQUET_PATH}
  }
}
```

[Other available configuration for Parquet can be found here](https://spark.apache.org/docs/latest/sql-data-sources-parquet.html#data-source-option)

#### Delta

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

```
jdbc {
    postgres {
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

Ensure that the user has write permission so it is able to save the table to the target tables.
<details>

```sql
GRANT INSERT ON <schema>.<table> TO <user>;
```

</details>

#### Postgres

##### Permissions

Following permissions are required when generating plan and tasks:
<details>

```sql
GRANT SELECT ON information_schema.tables TO < user >;
GRANT SELECT ON information_schema.columns TO < user >;
GRANT SELECT ON information_schema.key_column_usage TO < user >;
GRANT SELECT ON information_schema.table_constraints TO < user >;
GRANT SELECT ON information_schema.constraint_column_usage TO < user >;
```

</details>

#### MySQL

##### Permissions

Following permissions are required when generating plan and tasks:
<details>

```sql
GRANT SELECT ON information_schema.columns TO < user >;
GRANT SELECT ON information_schema.statistics TO < user >;
GRANT SELECT ON information_schema.key_column_usage TO < user >;
```

</details>

### Cassandra

Follows same configuration as defined by the Spark Cassandra Connector as
found [here](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md)

```
org.apache.spark.sql.cassandra {
    cassandra {
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

Ensure that the user has write permission so it is able to save the table to the target tables.
<details>

```sql
GRANT INSERT ON <schema>.<table> TO <user>;
```

</details>

### JMS

Uses JNDI lookup to send messages to JMS queue. Ensure that the messaging system you are using has your queue/topic
registered
via JNDI otherwise a connection cannot be created.

```
jms {
    solace {
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

Define a URL to connect to when sending HTTP requests.  
Later, can have the ability to define generated data as part of the URL.

```
http {
    customer_api {
        url = "http://localhost:80/get"
        url = ${?HTTP_URL}
        user = "admin"      #optional
        user = ${?HTTP_USER}
        password = "admin"  #optional
        password = ${?HTTP_PASSWORD}
    }
}
```
