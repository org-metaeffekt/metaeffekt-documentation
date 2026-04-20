# {metæffekt} Inventory Index

## Introduction

{metæffekt} Inventory Index is an application that allows importing inventories into a database and
querying their data through API requests. It consists of four modules that all have a dedicated focus and logic,
such as the import of inventories, the definition of the query language and more.

## Concept and Focus
The main focus for the {metæffekt} Inventory Index is to store inventory data in a database efficiently and prevent having redundant data as much as possible.


### Redundant-free storing data
To ensure redundant-free persistence of data, we used hashes. Each entity has got its own hash value calculated and persisted with them. As a result no duplicates are persisted.
Now if two entities have the same hash, their field values are compared one by one and the right entity is then derived.

## Prerequirements and initial setup
[Installation Readme](installation/README.md)

## Modules

### ae-inventory-importer-service
[Importer Service Readme](ae-inventory-importer-service/README.md)

### ae-inventory-index

### ae-inventory-query-language

### ae-inventory-query-service
[Query Service Readme](ae-inventory-query-service/README.md)
