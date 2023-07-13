# Data Caterer - Data Generator

## Overview

Ability to generate production like data based on any source/target system whether it be a CSV file, database table, etc.
Just define your data source connections and data will be generated.
It can also be manually altered to produce data or scenarios the way you want.
  
<video src="https://github.com/pflooky/data-caterer-docs/assets/26299147/d853241b-7c7e-4943-aefe-4002b848edf5" controls="controls" style="max-width: fit-content;">
</video>
  
![Data Caterer data flow flags](tech/diagrams/data_flow_flags.drawio.png "Data Flow flags")

## Generate data

### Quick start

#### Docker

1. `docker run -v /tmp:/opt/app/data-caterer pflookyy/data-caterer-basic:0.1`
2. Check `/tmp` folder for generated JSON data

### Manually create Plan and Task(s)

1. Create plan like [here](../sample/plan/simple-json-plan.yaml)
2. Create tasks like [here](../sample/task)
3. Create application configuration like [here](../sample/conf/application.conf)
4. Run via docker `docker run -e PLAN_FILE_PATH= -e TASK_FOLDER_PATH= pflookyy/data-caterer-basic:0.1`

