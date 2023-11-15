# Group By Validation

If you want to run aggregations based on a particular set of columns or just the whole dataset, you can do so via group
by validations. An example would be checking that the sum of `amount` is less than 1000 per `account_id, year`. The
validations applied can be one of the validations from the [basic validation set found here](basic-validation.md).

## Record count

Check the number of records across the whole dataset.

=== "Java"

    ```java
    validation().groupBy().count().lessThan(1000)
    ```

=== "Scala"

    ```scala
    validation.groupBy().count().lessThan(1000)
    ```

## Record count per group

Check the number of records for each group.

=== "Java"

    ```java
    validation().groupBy("account_id", "year").count().lessThan(10)
    ```

=== "Scala"

    ```scala
    validation.groupBy("account_id", "year").count().lessThan(10)
    ```

## Sum

Check the sum of a columns values for each group adheres to validation.

=== "Java"

    ```java
    validation().groupBy("account_id", "year").sum("amount").lessThan(1000)
    ```

=== "Scala"

    ```scala
    validation.groupBy("account_id", "year").sum("amount").lessThan(1000)
    ```

## Count

Check the count for each group adheres to validation.

=== "Java"

    ```java
    validation().groupBy("account_id", "year").count("amount").lessThan(10)
    ```

=== "Scala"

    ```scala
    validation.groupBy("account_id", "year").count("amount").lessThan(10)
    ```

## Min

Check the min for each group adheres to validation.

=== "Java"

    ```java
    validation().groupBy("account_id", "year").min("amount").greaterThan(0)
    ```

=== "Scala"

    ```scala
    validation.groupBy("account_id", "year").min("amount").greaterThan(0)
    ```

## Max

Check the max for each group adheres to validation.

=== "Java"

    ```java
    validation().groupBy("account_id", "year").max("amount").lessThanOrEqual(100)
    ```

=== "Scala"

    ```scala
    validation.groupBy("account_id", "year").max("amount").lessThanOrEqual(100)
    ```

## Average

Check the average for each group adheres to validation.

=== "Java"

    ```java
    validation().groupBy("account_id", "year").avg("amount").between(40, 60)
    ```

=== "Scala"

    ```scala
    validation.groupBy("account_id", "year").avg("amount").between(40, 60)
    ```

## Standard deviation

Check the standard deviation for each group adheres to validation.

=== "Java"

    ```java
    validation().groupBy("account_id", "year").stddev("amount").between(0.5, 0.6)
    ```

=== "Scala"

    ```scala
    validation.groupBy("account_id", "year").stddev("amount").between(0.5, 0.6)
    ```
