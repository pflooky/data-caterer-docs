# Validations

Validations can be used to run data checks after you have run the data generator or even as a standalone task. A report
summarising the success or failure of the validations is produced and can be examined for further investigation.

Currently, SQL expression validations are supported (can see [here](https://spark.apache.org/docs/latest/api/sql/)
for reference what other expressions are valid), but will later be extended out to supported other validations such as
aggregates (group by account_number, sum of amounts should be greater than 100), ordering (transaction dates should be
in descending order), relationships (at least one transaction per account_number) or data profiling (how close produced
data profile is to expected data profile).

## Define Validations

### Expression Validation

=== "Java"

    ```java
    var csvTxns = csv("transactions", "/tmp/csv", Map.of("header", "true"))
      .validations(
        validation().expr("amount < 100"),
        validation().expr("year == 2021").errorThreshold(0.1),  //equivalent to if error percentage is > 10%, then fail
        validation().expr("REGEXP_LIKE(name, 'Peter .*')").errorThreshold(200)  //equivalent to if number of errors is > 200, then fail
      );
    
    var conf = configuration().enableValidation(true);
    ```

=== "Scala"

    ```scala
    val csvTxns = csv("transactions", "/tmp/csv", Map("header" -> "true"))
      .validations(
        validation.expr("amount < 100"),
        validation.expr("year == 2021").errorThreshold(0.1),  //equivalent to if error percentage is > 10%, then fail
        validation.expr("REGEXP_LIKE(name, 'Peter .*')").errorThreshold(200)  //equivalent to if number of errors is > 200, then fail
      )
    
    val conf = configuration.enableValidation(true)
    ```

=== "YAML"

    ```yaml
    ---
    name: "account_checks"
    dataSources:
      transactions:
        options:
          path: "/tmp/csv"
        validations:
          - expr: "amount < 100"
          - expr: "year == 2021"
            errorThreshold: 0.1   #equivalent to if error percentage is > 10%, then fail
          - expr: "REGEXP_LIKE(name, 'Peter .*')"
            errorThreshold: 200   #equivalent to if number of errors is > 200, then fail
            description: "Should be lots of Peters"

    #enableValidation inside application.conf
    ```

## Wait Condition

Once data has been generated, you may want to wait for a certain condition to be met before starting the data
validations. This can be via:
  
- Pause for seconds
- When file is available
- Data exists
- Webhook

### Pause

=== "Java"

    ```java
    var csvTxns = csv("transactions", "/tmp/csv", Map.of("header", "true"))
      .validationWait(waitCondition().pause(1));
    ```

=== "Scala"

    ```scala
    val csvTxns = csv("transactions", "/tmp/csv", Map("header" -> "true"))
      .validationWait(waitCondition.pause(1))
    ```

=== "YAML"

    ```yaml
    ---
    name: "account_checks"
    dataSources:
      transactions:
        options:
          path: "/tmp/csv"
        waitCondition:
          pauseInSeconds: 1
    ```

### Data exists

=== "Java"

    ```java
    var csvTxns = csv("transactions", "/tmp/csv", Map.of("header", "true"))
      .validationWaitDataExists("updated_date > DATE('2023-01-01')");
    ```

=== "Scala"

    ```scala
    val csvTxns = csv("transactions", "/tmp/csv", Map("header" -> "true"))
      .validationWaitDataExists("updated_date > DATE('2023-01-01')")
    ```

=== "YAML"

    ```yaml
    ---
    name: "account_checks"
    dataSources:
      transactions:
        options:
          path: "/tmp/csv"
        waitCondition:
          dataSourceName: "transactions"
          options:
            path: "/tmp/csv"
          expr: "updated_date > DATE('2023-01-01')"
    ```

### Webhook

=== "Java"

    ```java
    var csvTxns = csv("transactions", "/tmp/csv", Map.of("header", "true"))
      .validationWait(waitCondition().webhook("http://localhost:8080/finished")); //by default, GET request successful when 200 status code
    
    //or
    
    var csvTxnsWithStatusCodes = csv("transactions", "/tmp/csv", Map.of("header", "true"))
      .validationWait(waitCondition().webhook("http://localhost:8080/finished", "GET", 200, 202));  //successful if 200 or 202 status code

    //or
    
    var csvTxnsWithExistingHttpConnection = csv("transactions", "/tmp/csv", Map.of("header", "true"))
      .validationWait(waitCondition().webhook("my_http", "http://localhost:8080/finished"));  //use connection configuration from existing 'my_http' connection definition
    ```

=== "Scala"

    ```scala
    val csvTxns = csv("transactions", "/tmp/csv", Map("header" -> "true"))
      .validationWait(waitCondition.webhook("http://localhost:8080/finished"))  //by default, GET request successful when 200 status code

    //or

    val csvTxnsWithStatusCodes = csv("transactions", "/tmp/csv", Map("header" -> "true"))
      .validationWait(waitCondition.webhook("http://localhost:8080/finished", "GET", 200, 202)) //successful if 200 or 202 status code

    //or

    val csvTxnsWithExistingHttpConnection = csv("transactions", "/tmp/csv", Map("header" -> "true"))
      .validationWait(waitCondition.webhook("my_http", "http://localhost:8080/finished")) //use connection configuration from existing 'my_http' connection definition
    ```

=== "YAML"

    ```yaml
    ---
    name: "account_checks"
    dataSources:
      transactions:
        options:
          path: "/tmp/csv"
        waitCondition:
          url: "http://localhost:8080/finished" #by default, GET request successful when 200 status code

    #or
    
    ---
    name: "account_checks"
    dataSources:
      transactions:
        options:
          path: "/tmp/csv"
        waitCondition:
          url: "http://localhost:8080/finished"
          method: "GET"
          statusCodes: [200, 202] #successful if 200 or 202 status code

    #or
    
    ---
    name: "account_checks"
    dataSources:
      transactions:
        options:
          path: "/tmp/csv"
        waitCondition:
          dataSourceName: "my_http" #use connection configuration from existing 'my_http' connection definition
          url: "http://localhost:8080/finished"
    ```

### File available

=== "Java"

    ```java
    var csvTxns = csv("transactions", "/tmp/csv", Map.of("header", "true"))
      .validationWait(waitCondition().file("/tmp/json"));
    ```

=== "Scala"

    ```scala
    val csvTxns = csv("transactions", "/tmp/csv", Map.of("header", "true"))
      .validationWait(waitCondition.file("/tmp/json"))
    ```

=== "YAML"

    ```yaml
    ---
    name: "account_checks"
    dataSources:
      transactions:
        options:
          path: "/tmp/csv"
        waitCondition:
          path: "/tmp/json"
    ```

## Report

Once run, it will produce a report like [this](../../sample/docker/data/report/html/validations.html).
