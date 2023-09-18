# Foreign Keys

![Multiple data source foreign key example](../../diagrams/foreign_keys.drawio.png "Multiple data source foreign keys")

Foreign keys can be defined to represent the relationships between datasets where values are required to match for
particular columns.

## Single column

Define a column in one data source to match against another column.  
Below example shows a `postgres` data source with two tables, `accounts` and `transactions` that have a foreign key
for `account_id`.

=== "Java"

    ```java
    var postgresAcc = postgres("my_postgres", "jdbc:...")
      .table("public.accounts")
      .schema(
        field().name("account_id"),
        field().name("name"),
        ...
      );
    var postgresTxn = postgres(postgresAcc)
      .table("public.transactions")
      .schema(
        field().name("account_id"),
        field().name("full_name"),
        ...
      );
    
    plan().addForeignKeyRelationship(
      postgresAcc, "account_id",
      List.of(Map.entry(postgresTxn, "account_id"))
    );
    ```

=== "Scala"

    ```scala
    val postgresAcc = postgres("my_postgres", "jdbc:...")
      .table("public.accounts")
      .schema(
        field.name("account_id"),
        field.name("name"),
        ...
      )
    val postgresTxn = postgres(postgresAcc)
      .table("public.transactions")
      .schema(
        field.name("account_id"),
        field.name("full_name"),
        ...
      )

    plan.addForeignKeyRelationship(
      postgresAcc, "account_id",
      List(postgresTxn -> "account_id")
    )
    ```

=== "YAML"

    ```yaml
    ---
    name: "postgres_data"
    steps:
      - name: "accounts"
        type: "postgres"
        options:
          dbtable: "account.accounts"
        schema:
          fields:
            - name: "account_id"
            - name: "name"
      - name: "transactions"
        type: "postgres"
        options:
          dbtable: "account.transactions"
        schema:
          fields:
            - name: "account_id"
            - name: "full_name"
    ---
    name: "customer_create_plan"
    description: "Create customers in JDBC"
    tasks:
      - name: "postgres_data"
        dataSourceName: "my_postgres"

    sinkOptions:
      foreignKeys:
        "postgres.accounts.account_id":
          - "postgres.transactions.account_id"
    ```

## Multiple columns

You may have a scenario where multiple columns need to be aligned. From the same example, we want `account_id`
and `name` from `accounts` to match with `account_id` and `full_name` to match in `transactions` respectively.

=== "Java"

    ```java
    var postgresAcc = postgres("my_postgres", "jdbc:...")
      .table("public.accounts")
      .schema(
        field().name("account_id"),
        field().name("name"),
        ...
      );
    var postgresTxn = postgres(postgresAcc)
      .table("public.transactions")
      .schema(
        field().name("account_id"),
        field().name("full_name"),
        ...
      );
    
    plan().addForeignKeyRelationship(
      postgresAcc, List.of("account_id", "name"),
      List.of(Map.entry(postgresTxn, List.of("account_id", "full_name")))
    );
    ```

=== "Scala"

    ```scala
    val postgresAcc = postgres("my_postgres", "jdbc:...")
      .table("public.accounts")
      .schema(
        field.name("account_id"),
        field.name("name"),
        ...
      )
    val postgresTxn = postgres(postgresAcc)
      .table("public.transactions")
      .schema(
        field.name("account_id"),
        field.name("full_name"),
        ...
      )

    plan.addForeignKeyRelationship(
      postgresAcc, List("account_id", "name"),
      List(postgresTxn -> List("account_id", "full_name"))
    )
    ```

=== "YAML"

    ```yaml
    ---
    name: "postgres_data"
    steps:
      - name: "accounts"
        type: "postgres"
        options:
          dbtable: "account.accounts"
        schema:
          fields:
            - name: "account_id"
            - name: "name"
      - name: "transactions"
        type: "postgres"
        options:
          dbtable: "account.transactions"
        schema:
          fields:
            - name: "account_id"
            - name: "full_name"
    ---
    name: "customer_create_plan"
    description: "Create customers in JDBC"
    tasks:
      - name: "postgres_data"
        dataSourceName: "my_postgres"

    sinkOptions:
      foreignKeys:
        "my_postgres.accounts.account_id,name":
          - "my_postgres.transactions.account_id,full_name"
    ```

## Nested column

Your schema structure can have nested fields which can also be referenced as foreign keys. But to do so, you need to
create a proxy field that gets omitted from the final saved data.
  
In the example below, the nested `customer_details.name` field inside the `json` task needs to match with `name`
from `postgres`. A new field in the `json` called `_txn_name` is used as a temporary column to facilitate the foreign
key definition.

=== "Java"

    ```java
    var postgresAcc = postgres("my_postgres", "jdbc:...")
      .table("public.accounts")
      .schema(
        field().name("account_id"),
        field().name("name"),
        ...
      );
    var jsonTask = json("my_json", "/tmp/json")
      .schema(
        field().name("account_id"),
        field().name("customer_details")
          .schema(
            field().name("name").sql("_txn_name"), #nested field will get value from '_txn_name'
            ...
          ),
        field().name("_txn_name").omit(true)       #value will not be included in output
      );
    
    plan().addForeignKeyRelationship(
      postgresAcc, List.of("account_id", "name"),
      List.of(Map.entry(jsonTask, List.of("account_id", "_txn_name")))
    );
    ```

=== "Scala"

    ```scala
    val postgresAcc = postgres("my_postgres", "jdbc:...")
      .table("public.accounts")
      .schema(
        field.name("account_id"),
        field.name("name"),
        ...
      )
    var jsonTask = json("my_json", "/tmp/json")
      .schema(
        field.name("account_id"),
        field.name("customer_details")
          .schema(
            field.name("name").sql("_txn_name"), #nested field will get value from '_txn_name'
            ...
          ), 
        field.name("_txn_name").omit(true)       #value will not be included in output
      )

    plan.addForeignKeyRelationship(
      postgresAcc, List("account_id", "name"),
      List(jsonTask -> List("account_id", "_txn_name"))
    )
    ```

=== "YAML"

    ```yaml
    ---
    #postgres task yaml
    name: "postgres_data"
    steps:
      - name: "accounts"
        type: "postgres"
        options:
          dbtable: "account.accounts"
        schema:
          fields:
            - name: "account_id"
            - name: "name"
    ---
    #json task yaml
    name: "json_data"
    steps:
      - name: "transactions"
        type: "json"
        options:
          dbtable: "account.transactions"
        schema:
          fields:
            - name: "account_id"
            - name: "_txn_name"
              generator:
                options:
                  omit: true
            - name: "cusotmer_details"
              schema:
                fields:
                  name: "name"
                  generator:
                    type: "sql"
                    options:
                      sql: "_txn_name"

    ---
    #plan yaml
    name: "customer_create_plan"
    description: "Create customers in JDBC"
    tasks:
      - name: "postgres_data"
        dataSourceName: "my_postgres"
      - name: "json_data"
        dataSourceName: "my_json"

    sinkOptions:
      foreignKeys:
        "my_postgres.accounts.account_id,name":
          - "my_json.transactions.account_id,_txn_name"
    ```