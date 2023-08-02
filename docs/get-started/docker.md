# Run Data Caterer

## Docker

### Quick start

```shell
git clone git@github.com:pflooky/data-caterer-docs.git
cd docs/sample/docker
DATA_SOURCE=postgres docker-compose up -d datacaterer
```

You can change `DATA_SOURCE` to one of the following:
- postgres
- mysql
- cassandra
- solace
- kafka

If you want to test it out with your own setup, you can alter the corresponding files under [docs/sample/docker/data](../sample/docker/data)


### Run with multiple data sources (Postgres and CSV File)

```shell
PLAN=scenario-based DATA_SOURCE=postgres docker-compose up -d datacaterer
head data/custom/csv/transactions/part-00000*
sample_account=$(head -1 data/custom/csv/transactions/part-00000* | awk -F "," '{print $1}')
docker exec docker-postgres-1 psql -Upostgres -d customer -c "SELECT * FROM account.accounts WHERE account_number='$sample_account'"
```

You should be able to see the linked data between Postgres and the CSV file created along with 1 to 10 records per
account_id, name combination in the CSV file.

### Run with custom plan/task(s)

1. Create/alter plan under [`data/custom/plan`](../sample/docker/data/custom/plan/simple-json-plan.yaml)
2. Create/alter tasks under [`data/custom/task`](../sample/docker/data/custom/task/file/json/json-account-task.yaml)
3. Create/alter application configuration [`data/custom/application.conf`](../sample/docker/data/custom/application.conf)

```shell
DATA_SOURCE=<data source name> docker-compose up -d datacaterer
```

## Helm

[Link to sample helm on GitHub here](https://github.com/pflooky/data-caterer-docs/tree/main/helm/data-caterer)

Update the [configuration](https://github.com/pflooky/data-caterer-docs/blob/main/helm/data-caterer/templates/configuration.yaml)
to your own data connections and configuration.

```shell
git clone git@github.com:pflooky/data-caterer-docs.git
helm install data-caterer ./data-caterer-docs/helm/data-caterer
```
