# Validations

Validations can be used to run data checks after you have run the data generator or even as a standalone task. A report 
summarising the success or failure of the validations is produced and can be examined for further investigation.
  
Currently, SQL expression validations are supported (can see [here](https://spark.apache.org/docs/latest/api/sql/) 
for reference what other expressions are valid), but will later be extended out to supported other validations such as 
aggregates (group by account_number, sum of amounts should be greater than 100), ordering (transaction dates should be 
in descending order), relationships (at least one transaction per account_number) or data profiling (how close produced 
data profile is to expected data profile).

## Sample

=== "Java"

    ```java
    validationConfig()
      .name("account_checks")
      .description("Check account related fields have gone through system correctly")
      .addValidations(
        "accountJson",                          #data source name
        Map.of("path", "sample/json/txn-gen"),  #data source options
        validation().expr("amount < 100"),      #validations
        validation().expr("year == 2021").errorThreshold(0.1),
        validation().expr("regexp_like(name, 'Peter .*')").errorThreshold(200).description("Should be lots of Peters")
      );
    ```

=== "Scala"

    ```scala
    validationConfig
      .name("account_checks")
      .description("Check account related fields have gone through system correctly")
      .addValidations(
        "accountJson",                        #data source name
        Map("path" -> "sample/json/txn-gen"), #data source options
        validation.expr("amount < 100"),      #validations
        validation.expr("year == 2021").errorThreshold(0.1),
        validation.expr("regexp_like(name, 'Peter .*')").errorThreshold(200).description("Should be lots of Peters")
      )
    ```

=== "YAML"

    ```yaml
    ---
    name: "account_checks"
    description: "Check account related fields have gone through system correctly"
    dataSources:
      accountJson:
        options:
          path: "sample/json/txn-gen"
        validations:
          - expr: "amount < 100"
          - expr: "year == 2021"
            errorThreshold: 0.1   #equivalent to if error percentage is >= 10%, then fail
          - expr: "regexp_like(name, 'Peter .*')"
            errorThreshold: 200   #equivalent to if number of errors is >= 200, then fail
            description: "Should be lots of Peters"
    ```

Once run, it will produce a report like [this](../../sample/docker/data/report/html/validations.html).
