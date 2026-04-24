# {metæffekt} Inventory Index

## Introduction

{metæffekt} Inventory Index is an application that allows importing inventories into a database and
querying their data through API requests. It consists of four modules that all have a dedicated focus and logic,
such as the import of inventories, the definition of the query language and more.

## Concept and Focus

The main focus for the {metæffekt} Inventory Index is to efficiently store inventory data into a database and prevent having redundant data as much as
possible. This should allow the user to be able to query the database fast. To allow the user to query the database, the {metæffekt} Inventory Index
exports endpoints that can receive requests via an api and evaluate them.

### Redundant-free persistence

To ensure redundant-free persistence of data, calculated hashes are used. Each redundant-free entity has got its own hash value calculated and
persisted with it to prevent duplicates.
Two redundant-free approaches are distinguished between the persistence of inventories and entities.

Further details are provided in the [Index Readme](ae-inventory-index/README.md)

## Prerequirements and initial setup

To use the {metæffekt} Inventory Index some requirements have to be fulfilled first. After these the installation process can be started. Details
steps are described in the [Installation Readme](installation/README.md).

## Modules

The {metæffekt} Inventory Index has four modules that focus on dedicated aspects of the application.

### ae-inventory-importer-service

The ae inventory importer service is primarily responsible for finding new inventories and persisting these. Further details are provided in
the [Importer Service Readme](ae-inventory-importer-service/README.md).

### ae-inventory-index

The ae inventory index makes possible persisting inventories and also implements the scripts and main logic for setting up and managing the database
as well as providing the necessary views. For more details look at the [Index Readme](ae-inventory-index/README.md).

### ae-inventory-query-language

The ae inventory query language holds the definition and logic for defining the IQL (Inventory query language) and parsing the query. The details are
provided in the [Query Language Readme](ae-inventory-query-language/README.md).

### ae-inventory-query-service

The ae inventory query service exports the endpoint and implements security mechanisms to ensure a secure way of interaction between client and
server. More details are provided in the [Query Service Readme](ae-inventory-query-service/README.md).
