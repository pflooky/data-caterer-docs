# Configuration

A number of configurations can be made and customised within Data Caterer to help control what gets run and/or where any
metadata gets saved.

These configurations are defined from within your Java or Scala class via `configuration` or for YAML file setup,
`application.conf` file as seen 
[**here**](https://github.com/pflooky/data-caterer-example/blob/main/docker/data/custom/application.conf).

## Flags

Flags are used to control which processes are executed when you run Data Caterer.

| Config                         | Default | Paid | Description                                                                                                                                                                                                               |
|--------------------------------|---------|------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `enableGenerateData`           | true    | N    | Enable/disable data generation                                                                                                                                                                                            |
| `enableCount`                  | true    | N    | Count the number of records generated. Can be disabled to improve performance                                                                                                                                             |
| `enableFailOnError`            | true    | N    | Whilst saving generated data, if there is an error, it will stop any further data from being generated                                                                                                                    |
| `enableSaveReports`            | true    | N    | Enable/disable HTML reports summarising data generated, metadata of data generated (if `enableSinkMetadata` is enabled) and validation results (if `enableValidation` is enabled). Sample [**here**](generator/report.md) |
| `enableSinkMetadata`           | true    | N    | Run data profiling for the generated data. Shown in HTML reports if `enableSaveSinkMetadata` is enabled                                                                                                                   |
| `enableValidation`             | false   | N    | Run validations as described in plan. Results can be viewed from logs or from HTML report if `enableSaveSinkMetadata` is enabled. Sample [**here**](validation/validation.md)                                             |
| `enableGeneratePlanAndTasks`   | false   | Y    | Enable/disable plan and task auto generation based off data source connections                                                                                                                                            |
| `enableRecordTracking`         | false   | Y    | Enable/disable which data records have been generated for any data source                                                                                                                                                 |
| `enableDeleteGeneratedRecords` | false   | Y    | Delete all generated records based off record tracking (if `enableRecordTracking` has been set to true)                                                                                                                   |
| `enableGenerateValidations`    | false   | Y    | If enabled, it will generate validations based on the data sources defined.                                                                                                                                               |

=== "Java"

    ```java
    configuration()
      .enableGenerateData(true)
      .enableCount(true)
      .enableFailOnError(true)
      .enableSaveReports(true)
      .enableSinkMetadata(true)
      .enableValidation(false)
      .enableGeneratePlanAndTasks(false)
      .enableRecordTracking(false)
      .enableDeleteGeneratedRecords(false)
      .enableGenerateValidations(false);
    ```

=== "Scala"

    ```scala
    configuration
      .enableGenerateData(true)
      .enableCount(true)
      .enableFailOnError(true)
      .enableSaveReports(true)
      .enableSinkMetadata(true)
      .enableValidation(false)
      .enableGeneratePlanAndTasks(false)
      .enableRecordTracking(false)
      .enableDeleteGeneratedRecords(false)
      .enableGenerateValidations(false)
    ```

=== "application.conf"

    ```
    flags {
      enableCount = false
      enableCount = ${?ENABLE_COUNT}
      enableGenerateData = true
      enableGenerateData = ${?ENABLE_GENERATE_DATA}
      enableFailOnError = true
      enableFailOnError = ${?ENABLE_FAIL_ON_ERROR}
      enableGeneratePlanAndTasks = false
      enableGeneratePlanAndTasks = ${?ENABLE_GENERATE_PLAN_AND_TASKS}
      enableRecordTracking = false
      enableRecordTracking = ${?ENABLE_RECORD_TRACKING}
      enableDeleteGeneratedRecords = false
      enableDeleteGeneratedRecords = ${?ENABLE_DELETE_GENERATED_RECORDS}
      enableGenerateValidations = false
      enableGenerateValidations = ${?ENABLE_GENERATE_VALIDATIONS}
    }
    ```

## Folders

Depending on which flags are enabled, there are folders that get used to save metadata, store HTML reports or track the
records generated.

These folder pathways can be defined as a cloud storage pathway (i.e. `s3a://my-bucket/task`).

| Config                           | Default                                 | Paid | Description                                                                                                         |
|----------------------------------|-----------------------------------------|------|---------------------------------------------------------------------------------------------------------------------|
| `planFilePath`                   | /opt/app/plan/customer-create-plan.yaml | N    | Plan file path to use when generating and/or validating data                                                        |
| `taskFolderPath`                 | /opt/app/task                           | N    | Task folder path that contains all the task files (can have nested directories)                                     |
| `validationFolderPath`           | /opt/app/validation                     | N    | Validation folder path that contains all the validation files (can have nested directories)                         |
| `generatedReportsFolderPath`     | /opt/app/report                         | N    | Where HTML reports get generated that contain information about data generated along with any validations performed |
| `generatedPlanAndTaskFolderPath` | /tmp                                    | Y    | Folder path where generated plan and task files will be saved                                                       |
| `recordTrackingFolderPath`       | /opt/app/record-tracking                | Y    | Where record tracking parquet files get saved                                                                       |

=== "Java"

    ```java
    configuration()
      .planFilePath("/opt/app/custom/plan/postgres-plan.yaml")
      .taskFolderPath("/opt/app/custom/task")
      .validationFolderPath("/opt/app/custom/validation")
      .generatedReportsFolderPath("/opt/app/custom/report")
      .generatedPlanAndTaskFolderPath("/opt/app/custom/generated")
      .recordTrackingFolderPath("/opt/app/custom/record-tracking");
    ```

=== "Scala"

    ```scala
    configuration
      .planFilePath("/opt/app/custom/plan/postgres-plan.yaml")
      .taskFolderPath("/opt/app/custom/task")
      .validationFolderPath("/opt/app/custom/validation")
      .generatedReportsFolderPath("/opt/app/custom/report")
      .generatedPlanAndTaskFolderPath("/opt/app/custom/generated")
      .recordTrackingFolderPath("/opt/app/custom/record-tracking")
    ```

=== "application.conf"

    ```
    folders {
      planFilePath = "/opt/app/custom/plan/postgres-plan.yaml"
      planFilePath = ${?PLAN_FILE_PATH}
      taskFolderPath = "/opt/app/custom/task"
      taskFolderPath = ${?TASK_FOLDER_PATH}
      validationFolderPath = "/opt/app/custom/validation"
      validationFolderPath = ${?VALIDATION_FOLDER_PATH}
      generatedReportsFolderPath = "/opt/app/custom/report"
      generatedReportsFolderPath = ${?GENERATED_REPORTS_FOLDER_PATH}
      generatedPlanAndTaskFolderPath = "/opt/app/custom/generated"
      generatedPlanAndTaskFolderPath = ${?GENERATED_PLAN_AND_TASK_FOLDER_PATH}
      recordTrackingFolderPath = "/opt/app/custom/record-tracking"
      recordTrackingFolderPath = ${?RECORD_TRACKING_FOLDER_PATH}
    }
    ```

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

| Config                               | Default | Paid | Description                                                                                                                                                                                                             |
|--------------------------------------|---------|------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `numRecordsFromDataSource`           | 10000   | Y    | Number of records read in from the data source that could be used for data profiling                                                                                                                                    |
| `numRecordsForAnalysis`              | 10000   | Y    | Number of records used for data profiling from the records gathered in `numRecordsFromDataSource`                                                                                                                       |
| `oneOfMinCount`                      | 1000    | Y    | Minimum number of records required before considering if a field can be of type `oneOf`                                                                                                                                 |
| `oneOfDistinctCountVsCountThreshold` | 0.2     | Y    | Threshold ratio to determine if a field is of type `oneOf` (i.e. a field called `status` that only contains `open` or `closed`. Distinct count = 2, total count = 10, ratio = 2 / 10 = 0.2 therefore marked as `oneOf`) |
| `numGeneratedSamples`                | 10      | N    | Number of sample records from generated data to take. Shown in HTML report                                                                                                                                              |

=== "Java"

    ```Java
    configuration()
      .numRecordsFromDataSourceForDataProfiling(10000)
      .numRecordsForAnalysisForDataProfiling(10000)
      .oneOfMinCount(1000)
      .oneOfDistinctCountVsCountThreshold(1000)
      .numGeneratedSamples(10);
    ```

=== "Scala"

    ```scala
    configuration
      .numRecordsFromDataSourceForDataProfiling(10000)
      .numRecordsForAnalysisForDataProfiling(10000)
      .oneOfMinCount(1000)
      .oneOfDistinctCountVsCountThreshold(1000)
      .numGeneratedSamples(10)
    ```

=== "application.conf"

    ```
    metadata {
      numRecordsFromDataSource = 10000
      numRecordsForAnalysis = 10000
      oneOfMinCount = 1000
      oneOfDistinctCountVsCountThreshold = 0.2
      numGeneratedSamples = 10
    }
    ```

## Generation

When generating data, you may have some limitations such as limited CPU or memory, large number of data sources, or data
sources prone to failure under load.
To help alleviate these issues or speed up performance, you can control the number of records that get generated in each
batch.

| Config               | Default | Paid | Description                                                                                                                              |
|----------------------|---------|------|------------------------------------------------------------------------------------------------------------------------------------------|
| `numRecordsPerBatch` | 100000  | N    | Number of records across all data sources to generate per batch                                                                          |
| `numRecordsPerStep`  | <empty> | N    | Overrides the count defined in each step with this value if defined (i.e. if set to 1000, for each step, 1000 records will be generated) |

=== "Scala"

    ```scala
    configuration()
      .numRecordsPerBatch(100000)
      .numRecordsPerStep(1000);
    ```

=== "Scala"

    ```scala
    configuration
      .numRecordsPerBatch(100000)
      .numRecordsPerStep(1000)
    ```

=== "application.conf"

    ```
    generation {
      numRecordsPerBatch = 100000
      numRecordsPerStep = 1000
    }
    ```

## Runtime

Given Data Caterer uses Spark as the base framework for data processing, you can configure the job as to your 
specifications via configuration as seen [**here**](https://spark.apache.org/docs/latest/configuration.html).

=== "Java"

    ```java
    configuration()
      .master("local[*]")
      .runtimeConfig(Map.of("spark.driver.cores", "5"))
      .addRuntimeConfig("spark.driver.memory", "10g");
    ```

=== "Scala"

    ```scala
    configuration
      .master("local[*]")
      .runtimeConfig(Map("spark.driver.cores" -> "5"))
      .addRuntimeConfig("spark.driver.memory" -> "10g")
    ```

=== "application.conf"

    ```
    runtime {
      master = "local[*]"
      master = ${?DATA_CATERER_MASTER}
      config {
        "spark.driver.cores" = "5"
        "spark.driver.memory" = "10g"
      }
    }
    ```

