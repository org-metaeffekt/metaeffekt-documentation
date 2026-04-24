# Index

## Introduction

The ae inventory index is primarily responsible for importing and persisting the inventories and creating the views for the request.

The index focuses on some key features which are:

- redundant-free persistence
- linking tables for flexible entity relation management
- views to aggregate data from tables and satisfy the request
- use of indexes on UID's and hashes to increase query performance

## Redundant-free persistence

To ensure fast and efficient database performance, the inventory index focuses on redundant-free persistence of entities. The idea is that each entity
has its own UID and hash value. The hash value is used to identify identical entities and not persist them multiple times. The UID on the other hand
is
used to reference an entity when it is needed, f.e. in linking tabless.
The inventory index distinguishes between two approaches when it comes to redundant-free persistence: persisting inventories and persisting entities.

### Inventory

When a new inventory has to be persisted, first the inventory file hash is computed.
Next, the database is checked whether inventories with the calculated hash already exist.

If so, in the next step the source file names of each found inventory are compared to the currently to be imported inventory.

If no same source file name is found or no inventory with the same hash from the database is found, the current inventory file gets persisted.

If a matching source file name is derived, the first inventory from the database for which this was true is returned as a reference and the process of
persisting the inventory stops.

The following diagram shows the process in a simplified way:
![Import inventory](docs/images/import-inventory.svg)

### Entities

When an entity, f.e. an artifact, vulnerability, asset or assessment has to be persisted, first it is converted to an internal object and the hash
value for this entity is calculated.
After that the entity is stored in a map that has its hash value as the key.
During this process, if another identical entity (same hash, same fields) is to be persisted, the new entity will not be stored in the map again.

After all entities are been looped over and stored in the map, at the end of a transaction those are persisted to the database.
During this, the database is checked for entities having the same hash as the entity to be persisted.
If their hash values are same, their field values are also compared (because two different entities could have the same hash).
If their fields equal too, the UID of the first matching entity from the database is attached to the entity that was to be persisted.
In all other cases the entity is persisted.

## Linking tables

Linking tables are used to build relations between two entities ("linking them"). To achieve this, the linking tables store the UID of the entities
that have to be linked. The linking table entities are also hashed and persisted redundant-free. This allows the database to share the UID of a link
between different inventories when both have the same relation between entities. The inventories have a dedicated column for each link table where
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

## Views

A key feature of the inventory index is the usage of views for query requests.
Views are handled like tables in the backend. Each view is a construct of multiple joined tables. Those tables are chosen depending on the request
type of the user to provide the requested data.

### Materialized views

The materialized views store the joined table data physically on the disk. In the inventory index they are used as "base views" on which bigger (non
materialized) views are constructed.
This concept has the advantage, that the key joins, which can get very large and inefficient depending on the size of the tables, don't have to be
calculated with every access to the views. This increases the speed when querying views.

#### Refresh of materialized views

One downside of materialized views is that they have to be refreshed manually to keep the data up to date and consistent.
To ensure that, after every persistence of an inventory the view is refreshed by the backend.

### Query views

Query views are the final views used for satisfying query requests. They are constructed by joining the base materialized views.
Internally, those views are treated like other entities with the difference that they are read-only.

## Indexes

To increase performance and achieve fast access on the tables when building joins or querying the linking tables and entities, on every entity UID,
referenced UID's in a link and hash columns an index is set.