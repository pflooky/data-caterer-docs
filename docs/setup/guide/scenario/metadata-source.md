# Metadata Source

!!! example "Info"

    Generating data based on an external metadata source is a paid feature.

Creating a data generator for Postgres tables and CSV file based on metadata stored in Marquez (
follows [OpenLineage API](https://openlineage.io/)).

## Requirements

- 10 minutes
- Git
- Gradle
- Docker

## Get Started

First, we will clone the data-caterer-example repo which will already have the base project setup required.

```shell
git clone git@github.com:pflooky/data-caterer-example.git
```

### Marquez Setup

Can follow the README found [**here**](https://github.com/MarquezProject/marquez) to help with setting up Marquez in
your local environment. This comes with an instance of Postgres which we will also be using as a data store for
generated data.

The command that was run for this example to help with setup of dummy data was `./docker/up.sh -a 5001 -m 5002 --seed`.

Check that the following [url](http://localhost:3000) shows some data like below once you click on `food_delivery`
from the `ns` drop down in the top right corner.

![Marquez dashboard](../../../diagrams/marquez_dashboard.png)

#### Postgres Setup

Since we will also be using the Marquez Postgres instance as a data source, we will set up a separate database to store 
the generated data in via:

```shell
docker exec marquez-db psql -Upostgres -c 'CREATE DATABASE food_delivery'
```

### Plan Setup

Create a new Java or Scala class.

- Java: `src/main/java/com/github/pflooky/plan/MyAdvancedMetadataSourceJavaPlanRun.java`
- Scala: `src/main/scala/com/github/pflooky/plan/MyAdvancedMetadataSourcePlanRun.scala`

Make sure your class extends `PlanRun`.

=== "Java"

    ```java
    import com.github.pflooky.datacaterer.java.api.PlanRun;
    ...
    
    public class MyAdvancedMetadataSourceJavaPlanRun extends PlanRun {
    }
    ```

=== "Scala"

    ```scala
    import com.github.pflooky.datacaterer.api.PlanRun
    ...
    
    class MyAdvancedMetadataSourcePlanRun extends PlanRun {
    }
    ```

#### Schema

We can point the schema of a data source to our Marquez instance. For the Postgres data source, we will point to a
`namespace`, which in Marquez or OpenLineage, represents a set of datasets. For the CSV data source, we will point to
a specific `namespace` and `dataset`.

##### Single Schema

=== "Java"

    ```java
    var csvTask = csv("my_csv", "/tmp/data/csv", Map.of("saveMode", "overwrite", "header", "true"))
            .schema(metadataSource().marquez("http://localhost:5001", "food_delivery", "public.categories"))
            .count(count().records(100));
    ```

=== "Scala"

    ```scala
    val csvTask = csv("my_csv", "/tmp/data/csv")
      .schema(metadataSource.marquez("http://localhost:5001", "food_delivery", "public.categories"))
      .count(count.records(100))
    ```

The above defines that the schema will come from Marquez, which is a type of metadata source that contains information
about schemas. Specifically, it points to the `food_delivery` namespace and `public.categories` dataset to retrieve the
schema information from.

##### Multiple Schemas

=== "Java"

    ```java
    var postgresTask = postgres("my_postgres", "jdbc:postgresql://host.docker.internal:5432/food_delivery", "postgres", "password", Map.of())
        .schema(metadataSource().marquez("http://localhost:5001", "food_delivery"))
        .count(count().records(10));
    ```

=== "Scala"

    ```scala
    val postgresTask = postgres("my_postgres", "jdbc:postgresql://localhost:5432/food_delivery", "postgres", "password")
      .schema(metadataSource.marquez("http://localhost:5001", "food_delivery"))
      .count(count.records(10))
    ```

We now have pointed this Postgres instance to produce multiple schemas that are defined under the `food_delivery`
namespace. Also note that we are using database `food_delivery` in Postgres to push our generated data to.

### Run

Let's try run and see what happens.

```shell
cd ..
./run.sh
#input class MyAdvancedMetadataSourceJavaPlanRun or MyAdvancedMetadataSourcePlanRun
#after completing
docker exec marquez-db psql -Upostgres -d food_delivery -c 'SELECT * FROM public.categories'
```

It should look something like this.

```shell
  id   |         name         | menu_id |     description
-------+----------------------+---------+----------------------
 73632 | mmq1ABUtoippzP       |   69948 | ZwDt0dE3OzaBsa
 18399 | E 4ZhXIzXaFs         |   99659 | 43iaicgG
 34623 | 6k                   |   99392 | XccwCcedDnz
 37476 | 6CJUhQTN             |    9518 | EIALimD
```

Let's also check if there is a corresponding record in the CSV file.

```shell
$ cat docker/sample/csv/account/part-0000* | grep ACC03093143
ACC03093143,2023,Nadine Heidenreich Jr.,"{\"account_id\":\"ACC03093143\",\"year\":2023,\"amount\":87990.37196728592,\"details\":{\"name\":\"Nadine Heidenreich Jr.\",\"first_txn_date\":\"2021-11-09\",\"updated_by\":{\"user\":\"YfEyJCe8ohrl0j IfyT\",\"time\":\"2022-09-26T20:47:53.404Z\"}},\"transactions\":[{\"txn_date\":\"2021-11-09\",\"amount\":97073.7914706189}]}"
```

Great! The account, year, name and payload look to all match up.

### Additional Topics

#### Order of execution

You may notice that the events are generated first, then the CSV file. This is because as part of the `execute`
function, we passed in the `kafkaTask` first, before the `csvTask`. You can change the order of execution by
passing in `csvTask` before `kafkaTask` into the `execute` function.
