# {metæffekt} Inventory Index

## Introduction

{metæffekt} Inventory Index is an application that allows importing inventories and persisting them into a database and to
query the data using a dedicated API. It consists of four modules having a dedicated focus:

* inventory persistence,
* scheduled import of inventories,
* the definition of the inventory query language, and
* the query service REST endpoints.

## Concept and Focus

The main focus for the {metæffekt} Inventory Index is to efficiently store inventory data into a database and prevent data redundancy.
This allows the user to query the database efficiently. The {metæffekt} Inventory Index enable the user to query the database and display the
structured responses.

### Efficient Storage of Time-Series

One key feature of the {metæffekt} Inventory Index is the efficient storage of time-series. Inventories at different points in time contain a
significant amount of redundant data. Therefor identical datasets are stored only once and their references are shared between inventories to fasten
databsae queries and prevent slower performance.                       

Further details are provided in the [Inventory Index Persistence](ae-inventory-index/README.md).

## Prerequisites and initial setup

To use the {metæffekt} Inventory Index some prerequisites have to be fulfilled first. With these in place, the installation process can be started.

Detailed steps are described in the [Installation Guide](installation/README.md).

## Modules

The {metæffekt} Inventory Index has four modules that focus on dedicated aspects of the application.

### Inventory Importer Service (ae-inventory-importer-service)

The Inventory Importer Service is primarily responsible for loading new inventories and persisting these. Further details are provided in
the [Inventory Importer Service](ae-inventory-importer-service/README.md).

### Inventory Index (ae-inventory-index)

The Inventory Index (also: Inventory Index Repository) makes possible persisting inventories and also implements the scripts and main logic for
setting up and managing the database
as well as providing the necessary views. For more details look at the [Inventory Index Repository](ae-inventory-index/README.md).

### Inventory Query Language (ae-inventory-query-language)

The Inventory Query Language holds the definition and logic for defining the IQL (Inventory query language) and parsing the query. The details are
provided in the [Inventory Query Language](ae-inventory-query-language/README.md).

### Inventory query Service (ae-inventory-query-service)

The Inventory Query Service exports the endpoint and implements security mechanisms to ensure a secure way of interaction between client and
server. More details are provided in the [inventory Query Service](ae-inventory-query-service/README.md).
