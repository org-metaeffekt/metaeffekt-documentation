# Views

## Introduction

A key feature of the inventory index is the usage of views for query requests.
Views are handled like tables in the backend. Each view is a construct of multiple joined tables. Those tables are chosen depending on the request
type of the user to provide the requested data.

## Materialized views

The materialized views store the joined table data physically on the disk. In the inventory index they are used as "base views" on which bigger (non
materialized) views are constructed.
This concept has the advantage, that the key joins, which can get very large and inefficient depending on the size of the tables, don't have to be
calculated with every access to the views. This increases the speed when querying views.

### Refresh of materialized views

One downside of materialized views is that they have to be refreshed manually to keep the data up to date and consistent. 
To ensure that, after every persistence of an inventory or an inventory batch the view is refreshed by the service.

## Query views

Query views are the final views used for satisfying query requests. They are constructed by joining the base materialized views.
Internally, the finale query views are treated like other entities with the difference that they are read-only.
