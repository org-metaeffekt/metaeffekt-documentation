# Inventory Importer Service

## Introduction

The Inventory Importer Service module implements a scheduler that is responsible for searching files in the "input-inventories"
directory that holds the inventories to be persisted. If an inventory is already imported, it will be skipped and not persisted in the database again.
The folder may also have child folders f.e. to group the inventories by a specific category (f.e. keycloak, openssl, ...).

## Interfaces

### Scheduler

The scheduler uses some properties during the process that can be configured in the env.rc file. The next sections describe them in detail.

#### Database URL

The database URL describes which database is used. This URL can be configured via the `II_DATABASE_URL` property in the env.rc file.

#### User name

The name of the database user can be configured via the `II_DATABASE_USER_NAME` property in the env.rc file. This user will be created during
the setup process and has limited permissions.

#### User password

The password for the database user can be configured via the `II_DATABASE_USER_PASSWORD` property in the env.rc file.

#### Batch size

The batch size defines how many inventories will be imported simultaneously and then persisted. By default, the batch size is set to 10.
It can be configured by setting the `II_SCHEDULER_BATCH_SIZE` property in the env.rc file.
After each inventory batch is persisted, the materialized views are refreshed to ensure data consistency.

#### Tenant ID

The tenant ID property describes who the tenant of the inventories is.
It can be configured by setting the `II_SCHEDULER_TENANT_ID` property in the env.rc file.
With this, the same inventories can be imported if they have different tenants.

#### Path

The Path property describes where the inventories that have to be persisted are located.
It can be configured by setting the `II_SCHEDULER_INPUT_DIR` property in the env.rc file.
The inventories can be located directly under the path and also under child folders.

#### Cron

The scheduler runs with a cron expression that defines the repeated execution time and can be configured in the env.rc file by setting the
`II_SCHEDULER_CRON` property.
For further information on how to create a cron expression visit https://crontab.cronhub.io/.
By default, the scheduler runs every minute. Each minute it triggers the scheduler to run. If a scheduler from the minute before is still running, the
new scheduler is skipped.

#### assetKeyMappings

The import process allows configuring a mapping for three asset keys, that map to the metaeffekt asset keys.
The following table shows the mappable keys and the corresponding environment variables:

| Metaeffekt asset key | Environment variable           | Description                                            |
|:---------------------|:-------------------------------|:-------------------------------------------------------|
| **externalAssetId**  | II_SCHEDULER_EXTERNAL_ASSET_ID | Maps the defined key to the metaeffekt externalAssetId | 
| **externalViewId**   | II_SCHEDULER_EXTERNAL_VIEW_ID  | Maps the defined key to the metaeffekt externalViewId  | 
| **pathInGroup**      | II_SCHEDULER_PATH_IN_GROUP     | Maps the defined key to the metaeffekt pathInGroup     |

Each value of those variables can be a list of possible keys separated by comma, f.e. "Key1, Key 2, Key 3". Each comma separated elements will be seen
as a String.
In this example there are three Keys: "Key1", "Key 2" and "Key 3".
The importer will map the first key in the list for that a value is found to the corresponding asset attribute. Here the order is relevant and defines
the priority
of the keys to be mapped.
For the example list "Key1, Key 2, Key 3" for externalAssetId mapping the importer first would try to map Key1 to the externalAssetId.
If the inventory has a corresponding "Key1" attribute in the asset sheet, the value would be returned and all other keys in the list would be
ignored (even if those were valid too).
If the inventory has no "Key1" asset attribute, the importer would try to find a "Key 2" attribute and so on. If the importer does not find any
mappable element in the list, the value will be null.
By default, each list of the three mentioned variables has the metaeffekt asset key as the first element. Any other key(s) can be defined by adding
element(s) to the lists.

### Refresh of Materialized views

The refresh of the materialized views happens at two moments during the scheduler process:

1. Once after a batch of inventories is persisted
2. Once after all inventory batches are persisted

## Processing approach

The diagram below presents a compact and simplified view on the scheduler process:

![Scheduler process](docs/images/scheduler-process.svg)

### Marker files

During the scheduler process marker files are generated for each inventory file and used to save the import status of the file and some additional
information about the import status.

`Note`: A marker file is stored in the JSON format and located next to where the inventory file is located and has the same name as the inventory file
with the additional file ending **.json**

#### Marker data

This data is stored in the marker files:

| Data                       | Value type | Description                                                                                                         |
|:---------------------------|:-----------|:--------------------------------------------------------------------------------------------------------------------|
| **fileHash**               | String     | The file hash of the inventory file to check if the file has changed                                                |
| **lastCheckTimestamp**     | Instant    | The timestamp of the last time the file was checked for import                                                      |
| **importSuccessTimestamp** | Instant    | The timestamp of the first time the file was successfully imported/persisted                                        |
| **currentStatus**          | Status     | The current status of the import. Can be either `IMPORTED` if the file was successfully imported otherwise `FAILED` |
| **errorMessage**           | String     | The error message if the import of the file failed, otherwise if it was successful this will be null                |

#### Checking if inventory import needs to be imported

When the importer finds an inventory file, it first checks if the file is valid and needs to be imported. This is done by checking if a marker file
for this inventory file exists. The following diagram shows the process of checking if an inventory has to be imported or not:

![Scheduler process](docs/images/inventory-needs-to-be-imported-check.drawio.svg)

`Note`: If an inventory is deleted from the database manually, the corresponding marker file also has to be deleted by hand. If not so, the importer
will not import the same (unchanged) file because a marker file with the status `IMPORTED` and the same file hash already exists for this file.

#### Updating markers after import

Depending on the import status of the inventories, for each of them the marker file is updated with the new file hash, the import status and
timestamp. In case the import fails, an error message is additionally saved to the marker file. 