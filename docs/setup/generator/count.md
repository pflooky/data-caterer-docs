# Record Count

There are options related to controlling the number of records generated that can help in generating the scenarios or data required.

## Total Count

Total count is the simplest as you define the total number of records you require for that particular step.
For example, in the below step, it will generate 1000 records for the CSV file  

=== "Java"

    ```java
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .count(1000);
    
    #or

    task()
      .name("csv_file")
      .steps(
        step()
          .name("transactions")
          .type("csv")
          .option("path", "app/src/test/resources/sample/csv/transactions")
          .count(1000)
      );
    ```

=== "Scala"

    ```scala
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .count(1000)
  
    #or

    task
      .name("csv_file")
      .step(
        step
          .name("transactions")
          .`type`("csv")
          .option("path", "app/src/test/resources/sample/csv/transactions")
          .count(1000)
      )
    ```

=== "YAML"

    ```yaml
    name: "csv_file"
    steps:
      - name: "transactions"
        type: "csv"
        options:
          path: "app/src/test/resources/sample/csv/transactions"
        count:
          total: 1000
    ```

## Generated Count

As like most things in data-caterer, the count can be generated based on some metadata.
For example, if I wanted to generate between 1000 and 2000 records, I could define that by the below configuration:

=== "Java"

    ```java
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .count(generator().min(1000).max(2000));
    ```

=== "Scala"

    ```scala
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .count(generator.min(1000).max(2000))
    ```

=== "YAML"

    ```yaml
    name: "csv_file"
    steps:
      - name: "transactions"
        type: "csv"
        options:
          path: "app/src/test/resources/sample/csv/transactions"
        count:
          generator:
            type: "random"
            options:
              min: 1000
              max: 2000
    ```

## Per Column Count

When defining a per column count, this allows you to generate records "per set of columns".
This means that for a given set of columns, it will generate a particular amount of records per combination of values for those columns.  

One example of this would be when generating transactions relating to a customer. A customer may be defined by columns `account_id, name`.
A number of transactions would be generated per `account_id,name`.  

You can also use a combination of the above two methods to generate the number of records per column.

### Total

When defining a total count within the `perColumn` configuration, it translates to only creating `(count.total * count.perColumnTotal)` records.  
This is a fixed number of records that will be generated each time, with no variation between runs.

In the example below, we have `count.total = 1000` and `count.perColumnTotal = 2`. Which means that `1000 * 2 = 2000` records will be generated
for this CSV file every time data gets generated.

=== "Java"

    ```java
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .count(
        count()
          .total(1000)
          .perColumnTotal(2, "account_id", "name")
      );
    ```

=== "Scala"

    ```scala
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .count(
        count
          .total(1000)
          .perColumnTotal(2, "account_id", "name")
      )
    ```

=== "YAML"

    ```yaml
    name: "csv_file"
    steps:
      - name: "transactions"
        type: "csv"
        options:
          path: "app/src/test/resources/sample/csv/transactions"
        count:
          total: 1000
          perColumn:
            total: 2
            columnNames:
              - "account_id"
              - "name"
    ```

### Generated

You can also define a generator for the count per column. This can be used in scenarios where you want a variable number of records
per set of columns.

In the example below, it will generate between `(count.total * count.perColumnGenerator.generator.min) = (1000 * 1) = 1000` and
`(count.total * count.perColumnGenerator.generator.max) = (1000 * 2) = 2000` records.


=== "Java"

    ```java
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .count(
        count()
          .total(1000)
          .perColumnGenerator(generator().min(1).max(2), "account_id", "name")
      );
    ```

=== "Scala"

    ```scala
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .count(
        count
          .total(1000)
          .perColumnGenerator(generator.min(1).max(2), "account_id", "name")
      )
    ```

=== "YAML"

    ```yaml
    name: "csv_file"
    steps:
      - name: "transactions"
        type: "csv"
        options:
          path: "app/src/test/resources/sample/csv/transactions"
        count:
          total: 1000
          perColumn:
            columnNames:
              - "account_id"
              - "name"
            generator:
              type: "random"
              options:
                max: 2
                min: 1
    ```
