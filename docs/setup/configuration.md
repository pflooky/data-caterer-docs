# Configuration

A number of configurations can be made and customised within Data Caterer to help control what gets run and/or where any
metadata gets saved.

These configurations are defined from within your `application.conf` file as
seen [here](../sample/docker/data/custom/application.conf).

## Flags

Flags are used to control which processes are executed when you run Data Caterer.

| Config                       | Default | Paid | Description                                                                                                                                                                       |
|------------------------------|---------|------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| enableGenerateData           | true    | N    | Enable/disable data generation                                                                                                                                                    |
| enableCount                  | true    | N    | Count the number of records generated. Can be disabled to improve performance                                                                                                     |
| enableFailOnError            | true    | N    | Whilst saving generated data, if there is an error, it will stop any further data from being generated                                                                            |
| enableSaveSinkMetadata       | true    | N    | Enable/disable HTML reports summarising data generated, metadata of data generated (if `enableSinkMetadata` is enabled) and validation results (if `enableValidation` is enabled) |
| enableSinkMetadata           | true    | N    | Run data profiling for the generated data. Shown in HTML reports if `enableSaveSinkMetadata` is enabled                                                                           |
| enableValidation             | true    | N    | Run validations as described in plan. Results can be viewed from logs or from HTML report if `enableSaveSinkMetadata` is enabled                                                  |
| enableGeneratePlanAndTasks   | false   | Y    | Enable/disable plan and task auto generation based off data source connections                                                                                                    |
| enableRecordTracking         | false   | Y    | Enable/disable which data records have been generated for any data source                                                                                                         |
| enableDeleteGeneratedRecords | false   | Y    | Delete all generated records based off record tracking (if `enableRecordTracking` has been set to true)                                                                           |

## Folders

Depending on which flags are enabled, there are folders that get used to save metadata, store HTML reports or track the
records generated.

These folder pathways can be defined as a cloud storage pathway (i.e. `s3a://my-bucket/task`).

| Config                         | Default                                 | Paid | Description                                                                                                         |
|--------------------------------|-----------------------------------------|------|---------------------------------------------------------------------------------------------------------------------|
| planFilePath                   | /opt/app/plan/customer-create-plan.yaml | N    | Plan file path to use when generating and/or validating data                                                        |
| taskFolderPath                 | /opt/app/task                           | N    | Task folder path that contains all the task files (can have nested directories)                                     |
| validationFolderPath           | /opt/app/validation                     | N    | Validation folder path that contains all the validation files (can have nested directories)                         |
| generatedDataResultsFolderPath | /opt/app/html                           | N    | Where HTML reports get generated that contain information about data generated along with any validations performed |
| generatedPlanAndTaskFolderPath | /tmp                                    | Y    | Folder path where generated plan and task files will be saved                                                       |
| recordTrackingFolderPath       | /opt/app/record-tracking                | Y    | Where record tracking parquet files get saved                                                                       |

## Metadata

When metadata gets generated, there are some configurations that can be altered to help with performance or accuracy
related issues.
Metadata gets generated from two processes: 1) if `enableGeneratePlanAndTasks` or 2) if `enableSinkMetadata` are
enabled.

During the generation of plan and tasks, data profiling is used to create the metadata for each of the fields defined in
the data source.
You may face issues if the number of records in the data source is large as data profiling is an expensive task.
Similarly, it can be expensive
when analysing the generated data if the number of records generated is large.

| Config                             | Default | Paid | Description                                                                                                                                                                                                             |
|------------------------------------|---------|------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| numRecordsFromDataSource           | 10000   | Y    | Number of records read in from the data source                                                                                                                                                                          |
| numRecordsForAnalysis              | 10000   | Y    | Number of records used for data profiling from the records gathered in `numRecordsFromDataSource`                                                                                                                       |
| oneOfDistinctCountVsCountThreshold | 0.1     | Y    | Threshold ratio to determine if a field is of type `oneOf` (i.e. a field called `status` that only contains `open` or `closed`. Distinct count = 2, total count = 10, ratio = 2 / 10 = 0.2 therefore marked as `oneOf`) |

## Generation

When generating data, you may have some limitations such as limited CPU or memory, large number of data sources, or data
sources prone to failure under load.
To help alleviate these issues or speed up performance, you can control the number of records that get generated in each
batch.

| Config             | Default | Paid | Description                                                     |
|--------------------|---------|------|-----------------------------------------------------------------|
| numRecordsPerBatch | 100000  | N    | Number of records across all data sources to generate per batch |
