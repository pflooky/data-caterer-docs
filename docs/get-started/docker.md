# Run Data Caterer

## Docker

### Quick start

```shell
git clone git@github.com:pflooky/data-caterer-docs.git
cd docs/sample/docker
docker-compose up -d datacaterer
```

To run for another data source, you can set `DATA_SOURCE` like below:
```shell
DATA_SOURCE=postgres docker-compose up -d datacaterer
```
  
Can set it to one of the following:
  
- postgres
- mysql
- cassandra
- solace
- kafka
- http

If you want to test it out with your own setup, you can alter the corresponding files under [docs/sample/docker/data](https://github.com/pflooky/data-caterer-docs/tree/main/docs/sample/docker/data)


### Run with multiple data sources (Postgres and CSV File)

```shell
PLAN=plan/scenario-based DATA_SOURCE=postgres docker-compose up -d datacaterer
head data/custom/csv/transactions/part-00000*
sample_account=$(head -1 data/custom/csv/transactions/part-00000* | awk -F "," '{print $1}')
docker exec docker-postgres-1 psql -Upostgres -d customer -c "SELECT * FROM account.accounts WHERE account_number='$sample_account'"
```

You should be able to see the linked data between Postgres and the CSV file created along with 1 to 10 records per
account_id, name combination in the CSV file.

### Run with custom data sources

1. Create/alter plan under [`data/custom/plan`](https://github.com/pflooky/data-caterer-docs/tree/main/docs/sample/docker/data/custom/plan)
2. Create/alter tasks under [`data/custom/task`](https://github.com/pflooky/data-caterer-docs/tree/main/docs/sample/docker/data/custom/task)
    1. Define your schemas and generator configurations such as record count
3. Create/alter application configuration [`data/custom/application.conf`](https://github.com/pflooky/data-caterer-docs/blob/main/docs/sample/docker/data/custom/application.conf)
    1. This is where you define your connection properties and other flags/configurations

```shell
DATA_SOURCE=<data source name> docker-compose up -d datacaterer
```

### Generate plan and tasks

```shell
APPLICATION_CONFIG_PATH=/opt/app/custom/application-dvd.conf ENABLE_GENERATE_DATA=false ENABLE_GENERATE_PLAN_AND_TASKS=true DATA_SOURCE=postgresdvd docker-compose up -d datacaterer
cat data/custom/generated/plan/plan_*
```

#### Generate data with record tracking

```shell
APPLICATION_CONFIG_PATH=/opt/app/custom/application-dvd.conf ENABLE_GENERATE_DATA=true ENABLE_GENERATE_PLAN_AND_TASKS=false ENABLE_RECORD_TRACKING=true DATA_SOURCE=postgresdvd PLAN=generated/plan/$(ls data/custom/generated/plan/ | grep plan | head -1 | awk -F " " '{print $NF}' | sed 's/\.yaml//g') docker-compose up -d datacaterer
```

#### Delete the generated data

```shell
APPLICATION_CONFIG_PATH=/opt/app/custom/application-dvd.conf ENABLE_GENERATE_DATA=false ENABLE_GENERATE_PLAN_AND_TASKS=false ENABLE_DELETE_GENERATED_RECORDS=true DATA_SOURCE=postgresdvd PLAN=generated/plan/$(ls data/custom/generated/plan/ | grep plan | head -1 | awk -F " " '{print $NF}' | sed 's/\.yaml//g') docker-compose up -d datacaterer
```

## Helm

[Link to sample helm on GitHub here](https://github.com/pflooky/data-caterer-docs/tree/main/helm/data-caterer)

Update the [configuration](https://github.com/pflooky/data-caterer-docs/blob/main/helm/data-caterer/templates/configuration.yaml)
to your own data connections and configuration.

```shell
git clone git@github.com:pflooky/data-caterer-docs.git
helm install data-caterer ./data-caterer-docs/helm/data-caterer
```
