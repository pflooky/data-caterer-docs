---
description: "Automatically generate data for OpenAPI/Swagger to HTTP endpoints."

---

# HTTP Source

!!! example "Info"

    Generating data based on OpenAPI/Swagger document and pushing to HTTP endpoint is a paid feature.

Creating a data generator based on an [OpenAPI/Swagger](https://spec.openapis.org/oas/latest.html) document.

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

### HTTP Setup

We will be using the [http-bin](https://httpbin.org/) docker image to help simulate a service with HTTP endpoints.

Start it via:

```shell
cd docker
docker-compose up -d http
docker ps
```

### Plan Setup

Create a new Java or Scala class.

- Java: `src/main/java/com/github/pflooky/plan/MyAdvancedHttpJavaPlanRun.java`
- Scala: `src/main/scala/com/github/pflooky/plan/MyAdvancedHttpPlanRun.scala`

Make sure your class extends `PlanRun`.

=== "Java"

    ```java
    import com.github.pflooky.datacaterer.java.api.PlanRun;
    ...
    
    public class MyAdvancedHttpJavaPlanRun extends PlanRun {
        {
            var conf = configuration().enableGeneratePlanAndTasks(true)
                .generatedReportsFolderPath("/opt/app/data/report");
        }
    }
    ```

=== "Scala"

    ```scala
    import com.github.pflooky.datacaterer.api.PlanRun
    ...
    
    class MyAdvancedHttpPlanRun extends PlanRun {
      val conf = configuration.enableGeneratePlanAndTasks(true)
        .generatedReportsFolderPath("/opt/app/data/report")
    }
    ```

We will enable generate plan and tasks so that we can read from external sources for metadata and save the reports
under a folder we can easily access.

#### Schema

We can point the schema of a data source to a OpenAPI/Swagger document or URL. For this example, we will use the OpenAPI
document found under `docker/mount/http/petstore.json` in the data-caterer-example repo. This is a simplified version of
the original OpenAPI spec that can be found [**here**](https://petstore.swagger.io/).

We have kept the following endpoints to test out:

- GET /pets - get all pets
- POST /pets - create a new pet
- GET /pets/{id} - get a pet by id
- DELETE /pets/{id} - delete a pet by id

##### Single Schema

=== "Java"

    ```java
    var httpTask = http("my_http")
            .schema(metadataSource().openApi("/opt/app/mount/http/petstore.json"))
            .count(count().records(2));
    ```

=== "Scala"

    ```scala
    val httpTask = http("my_http")
      .schema(metadataSource.openApi("/opt/app/mount/http/petstore.json"))
      .count(count.records(2))
    ```

The above defines that the schema will come from an OpenAPI document found on the pathway defined. It will then generate
2 requests per request method and endpoint combination.

### Run

Let's try run and see what happens.

```shell
cd ..
./run.sh
#input class MyAdvancedHttpJavaPlanRun or MyAdvancedHttpPlanRun
#after completing
docker logs -f docker-http-1
```

It should look something like this.

```shell
172.21.0.1 [06/Nov/2023:01:06:53 +0000] GET /anything/pets?tags%3DeXQxFUHVja+EYm%26limit%3D33895 HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:06:53 +0000] GET /anything/pets?tags%3DSXaFvAqwYGF%26tags%3DjdNRFONA%26limit%3D40975 HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:06:56 +0000] POST /anything/pets HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:06:56 +0000] POST /anything/pets HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:07:00 +0000] GET /anything/pets/kbH8D7rDuq HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:07:00 +0000] GET /anything/pets/REsa0tnu7dvekGDvxR HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:07:03 +0000] DELETE /anything/pets/EqrOr1dHFfKUjWb HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:07:03 +0000] DELETE /anything/pets/7WG7JHPaNxP HTTP/1.1 200 Host: host.docker.internal}
```

Looks like we have some data now. But we can do better and add some enhancements to it.

### Foreign keys

The four different requests that get sent could have the same `id` passed across to each of them if we define a foreign
key relationship. This will make it more realistic to a real life scenario as pets get created and queried by a
particular `id` value. We note that the `id` value is first used when a pet is created in the body of the POST request.
Then it gets used as a path parameter in the DELETE and GET requests.

To link them all together, we must follow a particular pattern when referring to request body, query parameter or path
parameter columns.

| HTTP Type       | Column Prefix | Example          |
|-----------------|---------------|------------------|
| Request Body    | `bodyContent` | `bodyContent.id` |
| Path Parameter  | `pathParam`   | `pathParamid`    |
| Query Parameter | `queryParam`  | `queryParamid`   |

Also note, that when creating a foreign field definition for a HTTP data source, to refer to a specific endpoint and
method, we have to follow the pattern of `{http method}{http path}`. For example, `POST/pets`. Let's apply this
knowledge to link all the `id` values together.

=== "Java"

    ```java
    var myPlan = plan().addForeignKeyRelationship(
            foreignField("my_http", "POST/pets", "bodyContent.id"),
            foreignField("my_http", "GET/pets/{id}", "pathParamid"),
            foreignField("my_http", "DELETE/pets/{id}", "pathParamid")
    );

    execute(myPlan, conf, httpTask);
    ```

=== "Scala"

    ```scala
    val myPlan = plan.addForeignKeyRelationship(
      foreignField("my_http", "POST/pets", "bodyContent.id"),
      foreignField("my_http", "GET/pets/{id}", "pathParamid"),
      foreignField("my_http", "DELETE/pets/{id}", "pathParamid")
    )

    execute(myPlan, conf, httpTask)
    ```

Let's test it out by running it again

```shell
./run.sh
#input class MyAdvancedHttpJavaPlanRun or MyAdvancedHttpPlanRun
docker logs -f docker-http-1
```

```shell
172.21.0.1 [06/Nov/2023:01:33:59 +0000] GET /anything/pets?limit%3D45971 HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:34:00 +0000] GET /anything/pets?limit%3D62015 HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:34:04 +0000] POST /anything/pets HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:34:05 +0000] POST /anything/pets HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:34:09 +0000] DELETE /anything/pets/5e HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:34:09 +0000] DELETE /anything/pets/IHPm2 HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:34:14 +0000] GET /anything/pets/IHPm2 HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:34:14 +0000] GET /anything/pets/5e HTTP/1.1 200 Host: host.docker.internal}
```

Now we have the same `id` values being produced across the POST, DELETE and GET requests! What if we knew that the `id` 
values should follow a particular pattern?

### Custom metadata

So given that we have defined a foreign key where the root of the foreign key values is from the POST request, we can 
update the metadata of the `id` column for the POST request and it will proliferate to the other endpoints as well. 
Given the `id` column is a nested column as noted in the foreign key, we can alter its metadata via the following:


=== "Java"

    ```java
    var httpTask = http("my_http")
            .schema(metadataSource().openApi("/opt/app/mount/http/petstore.json"))
            .schema(field().name("bodyContent").schema(field().name("id").regex("ID[0-9]{8}")))
            .count(count().records(2));
    ```

=== "Scala"

    ```scala
    val httpTask = http("my_http")
      .schema(metadataSource.openApi("/opt/app/mount/http/petstore.json"))
      .schema(field.name("bodyContent").schema(field.name("id").regex("ID[0-9]{8}")))
      .count(count.records(2))
    ```

We first get the column `bodyContent`, then get the nested schema and get the column `id` and add metadata stating that
`id` should follow the patter `ID[0-9]{8}`.

Let's try run again and hopefully we should see some proper ID values.

```shell
./run.sh
#input class MyAdvancedHttpJavaPlanRun or MyAdvancedHttpPlanRun
docker logs -f docker-http-1
```

```shell
172.21.0.1 [06/Nov/2023:01:45:45 +0000] GET /anything/pets?tags%3D10fWnNoDz%26limit%3D66804 HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:45:46 +0000] GET /anything/pets?tags%3DhyO6mI8LZUUpS HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:45:50 +0000] POST /anything/pets HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:45:51 +0000] POST /anything/pets HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:45:52 +0000] DELETE /anything/pets/ID55185420 HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:45:52 +0000] DELETE /anything/pets/ID20618951 HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:45:57 +0000] GET /anything/pets/ID55185420 HTTP/1.1 200 Host: host.docker.internal}
172.21.0.1 [06/Nov/2023:01:45:57 +0000] GET /anything/pets/ID20618951 HTTP/1.1 200 Host: host.docker.internal}
```

Great! Now we have replicated a production-like flow of HTTP requests.

Check out the full example under `AdvancedHttpPlanRun` in the example repo.
