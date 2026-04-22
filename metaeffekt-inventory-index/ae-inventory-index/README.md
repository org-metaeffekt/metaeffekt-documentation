# Index

## Import

### Inventory
When a new inventory has to be persisted, first the inventory file hash is computed. 
Next, the database is checked whether inventories with the calculated hash already exist.

If so, in the next step the source file names of each found inventory is compared to the currently to be imported inventory.

If no same source file name is found or no inventory with the same hash from the db is found, the current inventory file gets persisted.

If a matching sourde file name is derived, the first inventory from the database for which this was true is returned as a reference.

The following diagram shows the process in a simplified way:
![Import inventory](docs/images/import-inventory.svg)

### Entities
When an entity, f.e. an artifact, vulnerability, asset or assessment has to be persisted, first the hash value for this entity is calculated.
If two entities have the same hash, their field values are compared one by one and the right entity is then derived.
