# Inventory Index Repository

## Introduction

The Inventory Index module is primarily responsible for importing and persisting the inventories and creating the views for the request.

It focuses on central key features which are:

- low redundancy persistence
- linking tables for flexible dataset relation management
- views aggregating data from tables and satisfying the user request
- use of indexes on UID's and hashes to increase query performance

## Low Redundancy Storage

To ensure fast and efficient database queries, the inventory index uses a low redundancy approach for storing data. This allows storing identical
datasets only once in the database. Further information ist provided in [Persistence](./persistence/README.md).

## Linking Tables

Linking tables are used to build relations between two datasets ("linking them"). The linking table datasets are persisted redundancy-free. This
allows the database to share multiple link datasets between different inventories if these inventories hold the same dataset relation. Further
information ist provided in [Persistence](./persistence/README.md).

## Views

A key feature of the inventory index is the usage of views for query requests.
Each view is a construct of multiple joined tables. Those tables are chosen depending on the request
type of the user to provide the requested data. To ensure fast and efficient querying, materialized views are used to build the basic joins that
contain the most data and normal views then are used to join them and build the final view for the query. More information ist provided
in [Views](./views/README.md).

## Indexes

To increase performance and achieve fast access on the tables when building joins or querying the linking tables and datasets, on every dataset UID,
referenced UID's in links and hash columns in tables an index is set.xs