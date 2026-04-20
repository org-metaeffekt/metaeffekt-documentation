# Importer Service

## Introduction
The ae-inventory-importer-service module implements a scheduler that is responsible for scanning the "input-inventories" 
folder that holds the inventories and for persisting these. If an inventory is already imported, it will be skipped and not stored in the database again.
The folder may also have child folders grouping the inventories (f.e. keycloak, openssl, ...).


## Interfaces

### Scheduler
The scheduler uses three properties during the process that can be configured in the env.rc File.

#### Batch size:
The batch size defines, how many inventories will be imported simultaneously. By default, the batch size is set to 10. 
It can be configured by setting the `II_SCHEDULER_BATCH_SIZE` variable in the env.rc file. 
After each batch is imported, the materialized views are refreshed to ensure data consistency.

#### Path:
The Path variable describes where the inventories that have to be persisted are located.
It can be configured by setting the `II_SCHEDULER_INPUT_DIR` variable in the env.rc file.
The inventories can be located directly under the path and also under child folders.

#### Cron:
The scheduler runs with a cron expression that defines the repeated execution time and can be configured in the env.rc file by setting the `II_SCHEDULER_CRON` variable.
For further information on how to create a cron expression visit https://crontab.cronhub.io/.
By default, the scheduler runs every minute.

### Refresh of Materialized views
The refresh of the materialized views happens at two moments during the scheduler process:
1. Once after a batch is imported
2. Once after all batches are imported


### Processing approach
The diagram below shows a compact and simplified view on the scheduler process:

![Scheduler process](docs/images/scheduler-process.svg)

