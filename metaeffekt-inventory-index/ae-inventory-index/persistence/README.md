# Persistence

## Introduction

One key feature of the Inventory Index is the efficient persistence of datasets. Therefor some technical solutions were considered.
Those will be explained in more detail below.

## Hashes

To ensure that identical datasets (in our case: entities) are only stored once, they get a dedicated hash value composed of their field values. This
hash value is persisted with them.
The hash value then is used to check if a dataset already exists in the database or not.

## Persisting Inventories

When a new inventory has to be persisted, the following steps are carried out:

* First the inventory file hash is computed.
* The database is checked whether inventories with the calculated hash already exist.
    + If yes:
        + The source file names of each found inventory are compared to the currently to be imported inventory.
        + If a matching source file name is derived:
            + The first inventory from the database for which this was true is returned as a reference and the process of
              persisting the inventory stops.

* If no same source file name is found or no inventory with the same hash from the database is found, the current inventory file gets persisted.

The following diagram shows the process in a simplified way:
![Import inventory](../docs/images/import-inventory.svg)

## Persisting Entities

When an entity, f.e. an artifact, vulnerability, asset or assessment has to be persisted, the following steps are carried out:

* First, it is converted to an internal object and the hash value for this entity is calculated.
* Then the entity is stored in a redundancy-free map which has the entity's hash value as its key.
    + During this process, if another identical entity (same hash, same fields) is to be persisted, the new entity will not be stored in the map
      again if it's hash is already present in the map and the fields match.

After all entities are been looped over and stored in the map, at the end of a transaction those are persisted to the database.

The persistence process has the following steps:

* The database is checked for entities having the same hash as the entity to be persisted.
* If their hash values are same, their field values are also compared (because two different entities could have the same hash).
    * If their hashes and fields equal too, the UID of the first matching entity from the database is attached to the entity that is to be persisted
      and this entity does not get persisted.
* In all other cases the entity is persisted.

## Linking Tables

Linking tables are used to build relations between two entities ("linking them"). To achieve this, the linking tables store the UID of the entities to
be linked. The linking table entities are also hashed and persisted redundancy-free. This allows the database to share the UID of a link
between different inventories when these have the same relations between entities. The inventories have a dedicated column for each link table where
they store a list of links.

### Example

To understand how linking tables work the following example demonstrates how an artifact and vulnerability relation is represented in the database via
a linking table.

Entity Tables:

| uid      | artifact_name |  | uid      | vulnerability_name |      
|:---------|:--------------|:-|:---------|:-------------------|
| **3242** | artifactX     |  | **8989** | vulnerabilityX     |
| **2233** | artifactY     |  | **6796** | vulnerabilityY     |
| **2324** | artifactZ     |  | **8232** | vulnerabilityZ     |

Assuming **artifactX is affected by vulnerabilityZ** and **artifactY is affected by vulnerabilityX and also by vulnerabilityY**, the resulting linking
table would look like this:

Linking table:

| uid      | artifact_uid | vulnerability_uid |      
|:---------|:-------------|:------------------|
| **1111** | 3242         | 8232              |
| **2222** | 2233         | 8989              |
| **3333** | 2233         | 6796              |

If two different inventories (inventoryA and inventoryB) have the same relation between artifact and vulnerability, f.e. **artifactX is affected
by vulnerabilityZ**, then the inventory table would persist the UID of this relation (here its 1111) in the corresponding link reference column which
would look like this:

Inventory table:

| uid   | inventory_file_name | artifact_vulnerability_link |      
|:------|:--------------------|:----------------------------|
| **1** | inventoryA.xlsx     | [1111, 2222]                |
| **2** | inventoryB.xlsx     | [1111, 3333]                |

Here the relation between artifactY and vulnerabilityX is only present in inventoryA and the relation between artifactY and vulnerabilityY is only
present in inventoryB.