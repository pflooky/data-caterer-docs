# Run Data Caterer

## Docker

### Quick start

Ensure you have `docker` installed and running.

```shell
git clone git@github.com:pflooky/data-caterer-example.git
./run.sh
#check results under docker/sample/report/index.html folder
```

#### Report

Check the report generated under `docker/data/custom/report/index.html`.

Sample report can also be seen [**here**](../sample/report/html/index.html)

### Guided tour

Check out the started guide [**here**](../setup/guide/scenario/first-data-generation.md) that will take your through
step by step. You can also check the other guides [**here**](../setup/guide/index.md) to see the other possibilities of
what Data Caterer can achieve for you.

### Other data sources

To run for another data source, you can run using `docker-compose` and set `DATA_SOURCE` like below

```shell
./gradlew build
cd docker
DATA_SOURCE=postgres docker-compose up -d datacaterer
```

Can set it to one of the following:

- postgres
- mysql
- cassandra
- solace
- kafka
- http
