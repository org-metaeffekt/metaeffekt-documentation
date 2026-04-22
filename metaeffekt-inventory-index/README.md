# {metæffekt} Inventory Index

## Introduction

{metæffekt} Inventory Index is an application that allows importing inventories into a database and
querying their data through API requests. It consists of four modules that all have a dedicated focus and logic,
such as the import of inventories, the definition of the query language and more.

## Concept and Focus
The main focus for the {metæffekt} Inventory Index is to efficiently store inventory data into a database and prevent having redundant data as much as possible to enable fast database querying.


### Redundant-free persistence
To ensure redundant-free persistence of data, calculated hashes are used. Each redundant-free entity has got its own hash value calculated and persisted with it to prevent duplicates. 
Two redundant-free approaches are distinguished between the persistence of inventories and entities. 

Further details are provided in the [Index Readme](ae-inventory-index/README.md)

## Prerequirements and initial setup
[Installation Readme](installation/README.md)

## Modules

### ae-inventory-importer-service
[Importer Service Readme](ae-inventory-importer-service/README.md)

### ae-inventory-index
[Index Readme](ae-inventory-index/README.md)

### ae-inventory-query-language
[Query Language Readme](ae-inventory-query-language/README.md)

### ae-inventory-query-service
[Query Service Readme](ae-inventory-query-service/README.md)
