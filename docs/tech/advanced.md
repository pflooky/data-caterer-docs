# Advanced use cases

## Special data formats

There are many options available for you to use when you have a scenario when data has to be a certain format.

1. Create expression [datafaker](https://www.datafaker.net/documentation/expressions/)
   1. Can be used to create names, addresses, or anything that can be found
      under [here](sample/datafaker/expressions.txt)
2. Create regex

## Foreign keys across data sets

![Multiple data source foreign key example](../tech/diagrams/foreign_keys.drawio.png "Multiple data source foreign keys")

If you have a use case where you require a columns value to match in another data set, this can be achieved in the plan
definition.
For example, if I have the column `account_number` in a data source named `customer-postgres` and column `account_id`
in `transaction-cassandra`,

```yaml
sinkOptions:
  foreignKeys:
    #The foreign key name with naming convention [dataSourceName].[taskName].[columnName]
    "customer-postgres.accounts.account_number":
      #List of columns to match with same naming convention
      - "transaction-cassandra.transactions.account_id"
```

[Sample can be found here.](sample/plan/foreign-key-example-plan.yaml)
You can define any number of foreign key relationships as you want.

## Edge cases

For each given data type, there are edge cases which can cause issues when your application processes the data.
This can be controlled at a column level by including the following flag in the generator options:

```yaml
fields:
  - name: "amount"
    type: "double"
    generator:
      type: "random"
      options:
        enableEdgeCases: "true" 
```

If you want to know all the possible edge cases for each data
type, [can check the documentation here](../tech/generators.md).

## Scenario testing

You can create specific scenarios by adjusting the metadata found in the plan and tasks to your liking.  
For example, if you had two data sources, a Postgres database and a parquet file, and you wanted to save account data
into Postgres and transactions related to those accounts into a parquet file.
You can alter the `status` column in the account data to only generate `open` accounts
and define a foreign key between Postgres and parquet to ensure the same `account_id` is being used.  
Then in the parquet task, define 1 to 10 transactions per `account_id` to be generated.

[Postgres account generation example task](sample/task/jdbc/postgres/postgres-account-task.yaml)  
[Parquet transaction generation example task](sample/task/file/parquet/parquet-transaction-task.yaml)  
[Plan](sample/plan/scenario-based-plan.yaml)

## Generating JSON data
