# Run Data Caterer

## Quick start

Ensure you have `docker` installed and running.

```shell
git clone git@github.com:pflooky/data-caterer-example.git
cd data-caterer-example && ./run.sh
#check results under docker/sample/report/index.html folder
```

### Report

Check the report generated under `docker/data/custom/report/index.html`.

Sample report can also be seen [**here**](../sample/report/html/index.html)

## Paid Version Trial

30 day trial of the paid version can be accessed via these steps:

1. Join the [Slack Data Catering Slack group here](https://join.slack.com/t/data-catering/shared_invite/zt-2664ylbpi-w3n7lWAO~PHeOG9Ujpm~~w)
2. Get an API_KEY by using slash command `/token` in the Slack group (will only be visible to you)
3. 
        git clone git@github.com:pflooky/data-caterer-example.git
        cd data-caterer-example && export DATA_CATERING_API_KEY=<insert api key>
        ./run.sh

If you want to check how long your trial has left, you can check back in the Slack group or type `/token` again.

## Guided tour

Check out the starter guide [**here**](../setup/guide/scenario/first-data-generation.md) that will take your through
step by step. You can also check the other guides [**here**](../setup/guide/index.md) to see the other possibilities of
what Data Caterer can achieve for you.
