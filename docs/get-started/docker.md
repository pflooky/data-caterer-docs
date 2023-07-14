# Run Data Caterer

## Docker

### Quick start

Generate 1000 records of JSON data in local file system.

```shell
mkdir /tmp/datagen
docker run -v /tmp/datagen:/opt/app/data-caterer pflookyy/data-caterer:0.1
head /tmp/datagen/sample/json/account-gen/part-0000*
```

### Run with custom data connections

```shell
cp sample/conf/application.conf /tmp/datagen
vi /tmp/datagen/application.conf
docker run -v /tmp/datagen:/opt/app/data-caterer -e APPLICATION_CONFIG_PATH=/opt/app/datagen/application.conf pflookyy/data-caterer:0.1
```


### Run with plan and task auto generation



### Run with custom plan and task(s)

1. Create plan like [here](../sample/plan/simple-json-plan.yaml)
2. Create tasks like [here](../sample/task/file/json/json-account-task.yaml)
3. Create application configuration like [here](../sample/conf/application.conf)

```shell
cp sample/conf/application.conf /tmp/datagen
cp sample/plan/simple-json-plan.yaml /tmp/datagen
cp -R sample/task/ /tmp/datagen
#alter plan and task file(s) as required
docker run -v /tmp/datagen:/opt/app/data-caterer \
  -e APPLICATION_CONFIG_PATH=/opt/app/datagen/application.conf \
  -e PLAN_FILE_PATH=/opt/app/datagen/plan/simple-json-plan.yaml \
  -e TASK_FOLDER_PATH=/opt/app/datagen/task \
  pflookyy/data-caterer:0.1
```

## Helm

[Link to sample helm on Github here]()
