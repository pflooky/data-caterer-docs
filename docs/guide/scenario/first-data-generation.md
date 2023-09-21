# First Data Generation

Creating a data generator for a CSV file.

## Requirements

- 20 minutes
- Git
- Gradle
- Docker

## Get Started

First, we will clone the data-caterer-example repo which will already have the base project setup required.

```shell
git clone git@github.com:pflooky/data-caterer-example.git
```

### Plan Setup

Create a new Java or Scala class.

- Java: `src/main/java/com/github/pflooky/plan/MyCsvPlan.java`
- Scala: `src/main/scala/com/github/pflooky/plan/MyCsvPlan.scala`

Make sure your class extends `PlanRun`.

=== "Java"

    ```java
    import com.github.pflooky.datacaterer.java.api.PlanRun;
    
    public class MyCsvJavaPlan extends PlanRun {
    }
    ```

=== "Scala"

    ```scala
    import com.github.pflooky.datacaterer.api.PlanRun
    
    class MyCsvPlan extends PlanRun {
    }
    ```

This class defines where we need to define all of our configurations for generating data. There are helper variables and
methods defined to make it simple and easy to use.

#### Connection Configuration

When dealing with CSV files, we need to define a path for our generated CSV files to be saved at, along with any other
high level configurations.

=== "Java"

    ```java
    csv(
      "customer_accounts",              //name
      "/opt/app/data/customer/account", //path
      Map.of("header", "true")          //optional additional options
    )
    ```
    
    [Other additional options for CSV can be found here](https://spark.apache.org/docs/latest/sql-data-sources-csv.html#data-source-option)

=== "Scala"

    ```scala
    csv(
      "customer_accounts",              //name
      "/opt/app/data/customer/account", //path
      Map("header" -> "true")           //optional additional options
    )
    ```

    [Other additional options for CSV can be found here](https://spark.apache.org/docs/latest/sql-data-sources-csv.html#data-source-option)

#### Schema

Our CSV file that we generate should adhere to a defined schema where we can also define data types.

Let's define each field along with their corresponding data type. You will notice that the `string` fields do not have a
data type defined. This is because the default data type is `StringType`.

=== "Java"

    ```java
    var csvTask = csv("customer_accounts", "/opt/app/data/customer/account", Map.of("header", "true"))
            .schema(
                    field().name("account_id"),
                    field().name("amount").type(DoubleType.instance()),
                    field().name("created_by"),
                    field().name("name"),
                    field().name("open_time").type(TimestampType.instance()),
                    field().name("status")
            );
    ```

=== "Scala"

    ```scala
    val csvTask = csv("customer_accounts", "/opt/app/data/customer/account", Map("header" -> "true"))
      .schema(
        field.name("account_id"),
        field.name("amount").`type`(DoubleType),
        field.name("created_by"),
        field.name("name"),
        field.name("open_time").`type`(TimestampType),
        field.name("status")
      )
    ```

#### Field Metadata

We could stop here and generate random data for the accounts table. But wouldn't it be more useful if we produced data
that is closer to the structure of the data that would come in production? We can do this by defining various metadata
attributes that add guidelines that the data generator will understand when generating data.

##### account_id

- `account_id` follows a particular pattern that where it starts with `ACC` and has 8 digits after it.
  This can be defined via a regex like below. Alongside, we also mention that values are unique ensure that
  unique values are generated.

=== "Java"

    ```java
    field().name("account_id").regex("ACC[0-9]{8}").unique(true),
    ```

=== "Scala"

    ```scala
    field.name("account_id").regex("ACC[0-9]{8}").unique(true),
    ```

##### amount

- `amount` the numbers shouldn't be too large, so we can define a min and max for the generated numbers to be between
  `1` and `1000`.

=== "Java"

    ```java
    field().name("amount").type(DoubleType.instance()).min(1).max(1000),
    ```

=== "Scala"

    ```scala
    field.name("amount").`type`(DoubleType).min(1).max(1000),
    ```

##### name

- `name` is a string that also follows a certain pattern, so we could also define a regex but here we will choose to
  leverage the DataFaker library and create an `expression` to generate real looking name. All possible faker
  expressions
  can be found [here](../../sample/datafaker/expressions.txt)
  
=== "Java"

    ```java
    field().name("name").expression("#{Name.name}"),
    ```

=== "Scala"

    ```scala
    field.name("name").expression("#{Name.name}"),

##### open_time

- `open_time` is a timestamp that we want to have a value greater than a specific date. We can define a min date by
  using
  `java.sql.Date` like below.

=== "Java"

    ```java
    field().name("open_time").type(TimestampType.instance()).min(java.sql.Date.valueOf("2022-01-01")),
    ```

=== "Scala"

    ```scala
    field.name("open_time").`type`(TimestampType).min(java.sql.Date.valueOf("2022-01-01")),
    ```

##### status

- `status` is a field that can only obtain one of four values, `open, closed, suspended or pending`.

=== "Java"

    ```java
    field().name("status").oneOf("open", "closed", "suspended", "pending")
    ```

=== "Scala"

    ```scala
    field.name("status").oneOf("open", "closed", "suspended", "pending")
    ```

##### created_by

- `created_by` is a field that is based on the `status` field where it follows the
  logic: `if status is open or closed, then
  it is created_by eod else created_by event`. This can be achieved by defining a SQL expression like below.

=== "Java"

    ```java
    field().name("created_by").sql("CASE WHEN status IN ('open', 'closed') THEN 'eod' ELSE 'event' END"),
    ```

=== "Scala"

    ```scala
    field.name("created_by").sql("CASE WHEN status IN ('open', 'closed') THEN 'eod' ELSE 'event' END"),
    ```

Putting it all the fields together, our class should now look like this.

=== "Java"

    ```java
    var csvTask = csv("customer_accounts", "/opt/app/data/customer/account", Map.of("header", "true"))
            .schema(
                    field().name("account_id").regex("ACC[0-9]{8}").unique(true),
                    field().name("amount").type(DoubleType.instance()).min(1).max(1000),
                    field().name("created_by").sql("CASE WHEN status IN ('open', 'closed') THEN 'eod' ELSE 'event' END"),
                    field().name("name").expression("#{Name.name}"),
                    field().name("open_time").type(TimestampType.instance()).min(java.sql.Date.valueOf("2022-01-01")),
                    field().name("status").oneOf("open", "closed", "suspended", "pending")
            );
    ```

=== "Scala"

    ```scala
    val csvTask = csv("customer_accounts", "/opt/app/data/customer/account", Map("header" -> "true"))
      .schema(
        field.name("account_id").regex("ACC[0-9]{8}").unique(true),
        field.name("amount").`type`(DoubleType).min(1).max(1000),
        field.name("created_by").sql("CASE WHEN status IN ('open', 'closed') THEN 'eod' ELSE 'event' END"),
        field.name("name").expression("#{Name.name}"),
        field.name("open_time").`type`(TimestampType).min(java.sql.Date.valueOf("2022-01-01")),
        field.name("status").oneOf("open", "closed", "suspended", "pending")
      )
    ```

#### Additional Configurations

At the end of data generation, a report gets generated that summarises the actions it performed. We can control the
output folder of that report via configurations. We will also enable the unique check to ensure any unique fields will
have unique values generated.

=== "Java"

    ```java
    var config = configuration()
            .generatedReportsFolderPath("/opt/app/data/report")
            .enableUniqueCheck(true);
    ```

=== "Scala"

    ```scala
    val config = configuration
      .generatedReportsFolderPath("/opt/app/data/report")
      .enableUniqueCheck(true)
    ```

#### Execute

To tell Data Caterer that we want to run with the configurations along with the `csvTask`, we have to call `execute`
. So our full plan run will look like this.

=== "Java"

    ```java
    public class CsvJavaPlan extends PlanRun {
        {
            var csvTask = csv("customer_accounts", "/opt/app/data/customer/account", Map.of("header", "true"))
                    .schema(
                            field().name("account_id").regex("ACC[0-9]{8}").unique(true),
                            field().name("amount").type(DoubleType.instance()).min(1).max(1000),
                            field().name("created_by").sql("CASE WHEN status IN ('open', 'closed') THEN 'eod' ELSE 'event' END"),
                            field().name("name").expression("#{Name.name}"),
                            field().name("open_time").type(TimestampType.instance()).min(java.sql.Date.valueOf("2022-01-01")),
                            field().name("status").oneOf("open", "closed", "suspended", "pending")
                    );
            
            var config = configuration()
                    .generatedReportsFolderPath("/opt/app/data/report")
                    .enableUniqueCheck(true);
    
            execute(config, csvTask);
        }
    }
    ```

=== "Scala"

    ```scala
    class CsvPlan extends PlanRun {

      val csvTask = csv("customer_accounts", "/opt/app/data/customer/account", Map("header" -> "true"))
        .schema(
          field.name("account_id").regex("ACC[0-9]{8}").unique(true),
          field.name("amount").`type`(DoubleType).min(1).max(1000),
          field.name("created_by").sql("CASE WHEN status IN ('open', 'closed') THEN 'eod' ELSE 'event' END"),
          field.name("name").expression("#{Name.name}"),
          field.name("open_time").`type`(TimestampType).min(java.sql.Date.valueOf("2022-01-01")),
          field.name("status").oneOf("open", "closed", "suspended", "pending")
        )
        val config = configuration
          .generatedReportsFolderPath("/opt/app/data/report")
          .enableUniqueCheck(true)
        
        execute(config, csvTask)
    }
    ```

### Run

Now we can run via the script `./run.sh` that is in the top level directory of the `data-caterer-example` to run the
class we just
created.

```shell
./run.sh
#input class CsvJavaPlan or CsvPlan
#after completing
head docker/sample/customer/account/part-00000*
```

Your output should look like this.

```shell
account_id,amount,created_by,name,open_time,status
ACC06192462,853.9843359645766,eod,Hoyt Kertzmann MD,2023-07-22T11:17:01.713Z,closed
ACC15350419,632.5969895326234,eod,Dr. Claude White,2022-12-13T21:57:56.840Z,open
ACC25134369,592.0958847218986,eod,Fabian Rolfson,2023-04-26T04:54:41.068Z,open
ACC48021786,656.6413439322964,eod,Dewayne Stroman,2023-05-17T06:31:27.603Z,open
ACC26705211,447.2850352884595,event,Garrett Funk,2023-07-14T03:50:22.746Z,pending
ACC03150585,750.4568929015996,event,Natisha Reichel,2023-04-11T11:13:10.080Z,suspended
ACC29834210,686.4257811608622,event,Gisele Ondricka,2022-11-15T22:09:41.172Z,suspended
ACC39373863,583.5110618128994,event,Thaddeus Ortiz,2022-09-30T06:33:57.193Z,suspended
ACC39405798,989.2623959059525,eod,Shelby Reinger,2022-10-23T17:29:17.564Z,open
```

Also check the HTML report, found at `docker/sample/report/index.html`, that gets generated to get an overview of what
was executed.

![Sample report](../../sample/report/report_screenshot.png)
