# Data Generators

## Data Types

Below is a list of all supported data types for generating data:

| Data Type                 | Spark Data Type               | Options                                  | Description                                               |
|---------------------------|-------------------------------|------------------------------------------|-----------------------------------------------------------|
| string                    | StringType                    | `minLen, maxLen, expression, enableNull` |                                                           |
| integer                   | IntegerType                   | `min, max, stddev, mean`                 |                                                           |
| long                      | LongType                      | `min, max, stddev, mean`                 |                                                           |
| short                     | ShortType                     | `min, max, stddev, mean`                 |                                                           |
| decimal(precision, scale) | DecimalType(precision, scale) | `min, max, stddev, mean`                 |                                                           |
| double                    | DoubleType                    | `min, max, stddev, mean`                 |                                                           |
| float                     | FloatType                     | `min, max, stddev, mean`                 |                                                           |
| date                      | DateType                      | `min, max, enableNull`                   |                                                           |
| timestamp                 | TimestampType                 | `min, max, enableNull`                   |                                                           |
| boolean                   | BooleanType                   |                                          |                                                           |
| binary                    | BinaryType                    | `minLen, maxLen, enableNull`             |                                                           |
| byte                      | ByteType                      |                                          |                                                           |
| array                     | ArrayType                     | `arrayMinLen, arrayMaxLen, arrayType`    |                                                           |
| _                         | StructType                    |                                          | Implicitly supported when a schema is defined for a field |

## Options

### All data types

Some options are available to use for all types of data generators. Below is the list along with example and
descriptions:

| Option                | Default | Example                                                 | Description                                                                                                                                                                                                                                                                                        |
|-----------------------|---------|---------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `enableEdgeCase`      | false   | `enableEdgeCase: "true"`                                | Enable/disable generated data to contain edge cases based on the data type. For example, integer data type has edge cases of (Int.MaxValue, Int.MinValue and 0)                                                                                                                                    |
| `edgeCaseProbability` | 0.0     | `edgeCaseProb: "0.1"`                                   | Probability of generating a random edge case value if `enableEdgeCase` is true                                                                                                                                                                                                                     |
| `isUnique`            | false   | `isUnique: "true"`                                      | Enable/disable generated data to be unique for that column. Errors will be thrown when it is unable to generate unique data                                                                                                                                                                        |
| `seed`                | <empty> | `seed: "1"`                                             | Defines the random seed for generating data for that particular column. It will override any seed defined at a global level                                                                                                                                                                        |
| `sql`                 | <empty> | `sql: "CASE WHEN amount < 10 THEN true ELSE false END"` | Define any SQL statement for generating that columns value. Computation occurs after all non-SQL fields are generated. This means any columns used in the SQL cannot be based on other SQL generated columns. Data type of generated value from SQL needs to match data type defined for the field |

### String

| Option            | Default | Example                                                                                      | Description                                                                                                                                                                                                                |
|-------------------|---------|----------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `minLen`          | 1       | `minLen: "2"`                                                                                | Ensures that all generated strings have at least length `minLen`                                                                                                                                                           |
| `maxLen`          | 10      | `maxLen: "15"`                                                                               | Ensures that all generated strings have at most length `maxLen`                                                                                                                                                            |
| `expression`      | <empty> | `expression: "#{Name.name}"`<br/>`expression:"#{Address.city}/#{Demographic.maritalStatus}"` | Will generate a string based on the faker expression provided. All possible faker expressions can be found [here](../../sample/datafaker/expressions.txt)<br/> Expression has to be in format `#{<faker expression name>}` |
| `enableNull`      | false   | `enableNull: "true"`                                                                         | Enable/disable null values being generated                                                                                                                                                                                 |
| `nullProbability` | 0.0     | `nullProb: "0.1"`                                                                            | Probability to generate null values if `enableNull` is true                                                                                                                                                                |

**Edge cases**: ("", "\n", "\r", "\t", " ", "\\u0000", "\\ufff", "İyi günler", "Спасибо", "Καλημέρα", "صباح الخير", "
Förlåt", "你好吗", "Nhà vệ sinh ở đâu", "こんにちは", "नमस्ते", "Բարեւ", "Здравейте")

#### Sample

=== "Java"

    ```java
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .schema(
        field()
          .name("name")
          .type(StringType.instance())
          .expression("#{Name.name}")
          .enableNull(true)
          .nullProbability(0.1)
          .minLength(4)
          .maxLength(20)
      );
    ```

=== "Scala"

    ```scala
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .schema(
        field
          .name("name")
          .`type`(StringType)
          .expression("#{Name.name}")
          .enableNull(true)
          .nullProbability(0.1)
          .minLength(4)
          .maxLength(20)
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
        schema:
          fields:
            - name: "name"
              type: "string"
              generator:
                options:
                  expression: "#{Name.name}"
                  enableNull: true
                  nullProb: 0.1
                  minLength: 4
                  maxLength: 20
    ```

### Numeric

For all the numeric data types, there are 4 options to choose from: min, max and maxValue.
Generally speaking, you only need to define one of min or minValue, similarly with max or maxValue.  
The reason why there are 2 options for each is because of when metadata is automatically gathered, we gather the
statistics of the observed min and max values. Also, it will attempt to gather any restriction on the min or max value
as defined by the data source (i.e. max value as per database type).

#### Integer/Long/Short

| Option   | Default     | Example         | Description                                                          |
|----------|-------------|-----------------|----------------------------------------------------------------------|
| `min`    | 0           | `min: "2"`      | Ensures that all generated values are greater than or equal to `min` |
| `max`    | 1000        | `max: "25"`     | Ensures that all generated values are less than or equal to `max`    |
| `stddev` | 1.0         | `stddev: "2.0"` | Standard deviation for normal distributed data                       |
| `mean`   | `max - min` | `mean: "5.0"`   | Mean for normal distributed data                                     |

**Edge cases Integer**: (2147483647, -2147483648, 0)  
**Edge cases Long**: (9223372036854775807, -9223372036854775808, 0)  
**Edge cases Short**: (32767, -32768, 0)

##### Sample

=== "Java"

    ```java
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .schema(
        field().name("year").type(IntegerType.instance()).min(2020).max(2023),
        field().name("customer_id").type(LongType.instance()),
        field().name("customer_group").type(ShortType.instance())
      );
    ```

=== "Scala"

    ```scala
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .schema(
        field.name("year").`type`(IntegerType).min(2020).max(2023),
        field.name("customer_id").`type`(LongType),
        field.name("customer_group").`type`(ShortType)
      )
    ```

=== "YAML"

    ```yaml
    name: "csv_file"
    steps:
      - name: "transactions"
        ...
        schema:
          fields:
            - name: "year"
              type: "integer"
              generator:
                options:
                  min: 2020
                  max: 2023
            - name: "customer_id"
              type: "long"
            - name: "customer_group"
              type: "short"
    ```

#### Decimal


| Option             | Default     | Example           | Description                                                                                             |
|--------------------|-------------|-------------------|---------------------------------------------------------------------------------------------------------|
| `min`              | 0           | `min: "2"`        | Ensures that all generated values are greater than or equal to `min`                                    |
| `max`              | 1000        | `max: "25"`       | Ensures that all generated values are less than or equal to `max`                                       |
| `stddev`           | 1.0         | `stddev: "2.0"`   | Standard deviation for normal distributed data                                                          |
| `mean`             | `max - min` | `mean: "5.0"`     | Mean for normal distributed data                                                                        |
| `numericPrecision` | 10          | `precision: "25"` | The maximum number of digits                                                                            |
| `numericScale`     | 0           | `scale: "25"`     | The number of digits on the right side of the decimal point (has to be less than or equal to precision) |

**Edge cases Decimal**: (9223372036854775807, -9223372036854775808, 0)

##### Sample

=== "Java"

    ```java
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .schema(
        field().name("balance").type(DecimalType.instance()).numericPrecision(10).numericScale(5)
      );
    ```

=== "Scala"

    ```scala
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .schema(
        field.name("balance").`type`(DecimalType).numericPrecision(10).numericScale(5)
      )
    ```

=== "YAML"

    ```yaml
    name: "csv_file"
    steps:
      - name: "transactions"
        ...
        schema:
          fields:
            - name: "balance"
              type: "decimal"
                generator:
                  options:
                    precision: 10
                    scale: 5
    ```

#### Double/Float

| Option   | Default     | Example         | Description                                                          |
|----------|-------------|-----------------|----------------------------------------------------------------------|
| `min`    | 0.0         | `min: "2.1"`    | Ensures that all generated values are greater than or equal to `min` |
| `max`    | 1000.0      | `max: "25.9"`   | Ensures that all generated values are less than or equal to `max`    |
| `stddev` | 1.0         | `stddev: "2.0"` | Standard deviation for normal distributed data                       |
| `mean`   | `max - min` | `mean: "5.0"`   | Mean for normal distributed data                                     |

**Edge cases Double**: (+infinity, 1.7976931348623157e+308, 4.9e-324, 0.0, -0.0, -1.7976931348623157e+308, -infinity,
NaN)  
**Edge cases Float**: (+infinity, 3.4028235e+38, 1.4e-45, 0.0, -0.0, -3.4028235e+38, -infinity, NaN)

##### Sample

=== "Java"

    ```java
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .schema(
        field().name("amount").type(DoubleType.instance())
      );
    ```

=== "Scala"

    ```scala
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .schema(
        field.name("amount").`type`(DoubleType)
      )
    ```

=== "YAML"

    ```yaml
    name: "csv_file"
    steps:
      - name: "transactions"
        ...
        schema:
          fields:
            - name: "amount"
              type: "double"
    ```

### Date

| Option            | Default          | Example              | Description                                                          |
|-------------------|------------------|----------------------|----------------------------------------------------------------------|
| `min`             | now() - 365 days | `min: "2023-01-31"`  | Ensures that all generated values are greater than or equal to `min` |
| `max`             | now()            | `max: "2023-12-31"`  | Ensures that all generated values are less than or equal to `max`    |
| `enableNull`      | false            | `enableNull: "true"` | Enable/disable null values being generated                           |
| `nullProbability` | 0.0              | `nullProb: "0.1"`    | Probability to generate null values if `enableNull` is true          |

**Edge cases**: (0001-01-01, 1582-10-15, 1970-01-01, 9999-12-31)
([reference](https://github.com/apache/spark/blob/master/sql/catalyst/src/test/scala/org/apache/spark/sql/RandomDataGenerator.scala#L206))

#### Sample

=== "Java"

    ```java
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .schema(
        field().name("created_date").type(DateType.instance()).min(java.sql.Date.valueOf("2020-01-01"))
      );
    ```

=== "Scala"

    ```scala
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .schema(
        field.name("created_date").`type`(DateType).min(java.sql.Date.valueOf("2020-01-01"))
      )
    ```

=== "YAML"

    ```yaml
    name: "csv_file"
    steps:
      - name: "transactions"
        ...
        schema:
          fields:
            - name: "created_date"
              type: "date"
                generator:
                  options:
                    min: "2020-01-01"
    ```

### Timestamp

| Option            | Default          | Example                      | Description                                                          |
|-------------------|------------------|------------------------------|----------------------------------------------------------------------|
| `min`             | now() - 365 days | `min: "2023-01-31 23:10:10"` | Ensures that all generated values are greater than or equal to `min` |
| `max`             | now()            | `max: "2023-12-31 23:10:10"` | Ensures that all generated values are less than or equal to `max`    |
| `enableNull`      | false            | `enableNull: "true"`         | Enable/disable null values being generated                           |
| `nullProbability` | 0.0              | `nullProb: "0.1"`            | Probability to generate null values if `enableNull` is true          |

**Edge cases**: (0001-01-01 00:00:00, 1582-10-15 23:59:59, 1970-01-01 00:00:00, 9999-12-31 23:59:59)

#### Sample

=== "Java"

    ```java
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .schema(
        field().name("created_time").type(TimestampType.instance()).min(java.sql.Timestamp.valueOf("2020-01-01 00:00:00"))
      );
    ```

=== "Scala"

    ```scala
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .schema(
        field.name("created_time").`type`(TimestampType).min(java.sql.Timestamp.valueOf("2020-01-01 00:00:00"))
      )
    ```

=== "YAML"

    ```yaml
    name: "csv_file"
    steps:
      - name: "transactions"
        ...
        schema:
          fields:
            - name: "created_time"
              type: "timestamp"
                generator:
                  options:
                    min: "2020-01-01 00:00:00"
    ```

### Binary

| Option            | Default | Example              | Description                                                             |
|-------------------|---------|----------------------|-------------------------------------------------------------------------|
| `minLen`          | 1       | `minLen: "2"`        | Ensures that all generated array of bytes have at least length `minLen` |
| `maxLen`          | 20      | `maxLen: "15"`       | Ensures that all generated array of bytes have at most length `maxLen`  |
| `enableNull`      | false   | `enableNull: "true"` | Enable/disable null values being generated                              |
| `nullProbability` | 0.0     | `nullProb: "0.1"`    | Probability to generate null values if `enableNull` is true             |

**Edge cases**: ("", "\n", "\r", "\t", " ", "\\u0000", "\\ufff", -128, 127)

#### Sample

=== "Java"

    ```java
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .schema(
        field().name("payload").type(BinaryType.instance())
      );
    ```

=== "Scala"

    ```scala
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .schema(
        field.name("payload").`type`(BinaryType)
      )
    ```

=== "YAML"

    ```yaml
    name: "csv_file"
    steps:
      - name: "transactions"
        ...
        schema:
          fields:
            - name: "payload"
              type: "binary"
    ```

### Array

| Option            | Default | Example               | Description                                                                                                              |
|-------------------|---------|-----------------------|--------------------------------------------------------------------------------------------------------------------------|
| `arrayMinLen`     | 0       | `arrayMinLen: "2"`    | Ensures that all generated arrays have at least length `arrayMinLen`                                                     |
| `arrayMaxLen`     | 5       | `arrayMaxLen: "15"`   | Ensures that all generated arrays have at most length `arrayMaxLen`                                                      |
| `arrayType`       | <empty> | `arrayType: "double"` | Inner data type of the array. Optional when using Java/Scala API. Allows for nested data types to be defined like struct |
| `enableNull`      | false   | `enableNull: "true"`  | Enable/disable null values being generated                                                                               |
| `nullProbability` | 0.0     | `nullProb: "0.1"`     | Probability to generate null values if `enableNull` is true                                                              |

#### Sample

=== "Java"

    ```java
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .schema(
        field().name("last_5_amounts").type(ArrayType.instance()).arrayType("double")
      );
    ```

=== "Scala"

    ```scala
    csv("transactions", "app/src/test/resources/sample/csv/transactions")
      .schema(
        field.name("last_5_amounts").`type`(ArrayType).arrayType("double")
      )
    ```

=== "YAML"

    ```yaml
    name: "csv_file"
    steps:
      - name: "transactions"
        ...
        schema:
          fields:
            - name: "last_5_amounts"
              type: "array<double>"
    ```