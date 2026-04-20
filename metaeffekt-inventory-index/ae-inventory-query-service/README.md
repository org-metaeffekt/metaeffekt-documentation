# Query Service

## Interfaces

### Endpoints

Currently, the query service exports one endpoint which is the query endpoint.
This endpoint allows the client to query for vulnerabilities and their relating artifacts, assessments and assets and also for CPE's and their
relating vulnerabilities and artifacts.

### Request Schema

The request is sent by the client via the UI as a JSON object.
It has the following fields:

| Field                | Type          | Description                                                               |
|:---------------------|:--------------|:--------------------------------------------------------------------------|
| **filterExpression** | String        | defines the expression that then filteres the result set                  | 
| **page**             | int           | shows which page is currently shown                                       |
| **pageSize**         | int           | shows the currently defined page size                                     |
| **sorting**          | List<Sorting> | a list of field names to be sorted by and their direction (`ASC`, `DESC`) |
| **viewId**           | String        | the name of the requested view data                                       |

#### Token

The client has to provide a JWT token that includes claims. Those claims are a set of:

* tenants
* audiences
* projects
* labels
* components

Those claims are then evaluated by the backend and only the filtered data depending on the claims is returned as a result to the client.

### Response Schema
As a response of a clients request the server returns a queryResponseDTO object that includes the actual result object with data and additional properties for paging purposes.
The schema for the response object is as follows.

| Field                | Type             | Description                                                                                                                                                                                   |
|:---------------------|:-----------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **queryResponseDTO** | QueryResponseDTO | represents the resulting data object, composed from a list with the resulting rows themselfes (`queryResult`) and a `details` object that contains additional detail information for each row | 
| **size**             | long             | shows how many elements the resulting page has                                                                                                                                                | 
| **page**             | int              | shows which resulting page is currently shown                                                                                                                                                 |
| **pageSize**         | int              | shows the currently defined page size                                                                                                                                                         |
| **pageCount**        | int              | shows how many total pages the result has depending on the defined page size                                                                                                                  |

#### queryResponseDTO

The queryResponseDTO object consists of two parts: queryResult and details.
The queryResult is a list with the resulting row data from the requested view represented by an object (`ResultDTO`).
The details object contains additional data for each row. All details have a unique identifier that is used to reference them in
the queryResult list objects.

###### queryResult

The queryResult list stores elements of type `ResultDTO`. Depending on the selected view, this `ResultDTO` object is dynamically extended with additional
fields.
For example the `ResultDTO` for the`asset-vulnerable-component` view contains all fields from the `asset-vulnerability-assessment` view `ResultDTO` but
also includes additional fields for CPE data, like `cpeProduct`, `cpeVendor` and so on.

Aside from those view dependant fields, every `ResultDTO` object contains a `detailsRefs` object.
This object includes four lists, that store the details id references for this resulting row object.
Those four lists are:
* `artifactDetailRef`
* `vulnerabilityDetailRef`
* `assetDetailRef`
* `assessmentDetailRef`

This reference tracking is needed to prevent redundant saving of details in every `ResultDTO` and large JSON objects.

###### details

Currently, there are four detail objects. Those are:

* `artifactDetails`
* `vulnerabilityDetails`
* `assetDetails`
* `assessmentDetails`

For each of those detail objects separate unique ids are stored to prevent redundancy.

##### Examples
...

### IQL

The IQL (Inventory Query Language) is a custom language that uses expressions and operators to allow querying the inventory data.
It is case-insensitive and whitespaces are skipped.

#### Valid Expression

Valid expressions can be built by:

* Any valid `clause`
* Any expression concatenated by a `NOT`, `AND` or `OR`
* Any expression wrapped in braces `(`, `)`

##### Clause

A `clause` can be build in three ways.

1. Using a comparison operator that has a field name on its left side and a single value on its right side to enable comparing the field value with
   the given value:

   field _comparison_operator_ value.
2. Using a set operator that has a field name on its left side and a set on its right side to check if a field value is contained in the defined set:
   
   field _set_operator_ set.
3. Using an emptiness operator that only has a field name on its left side to check whether a field value is null or empty: 

   field _emptiness_operator_.

The `field` is currently Written in camel case.
The operators are described further in the next section.

#### Valid Operators

There are three types of valid operators. Those are `comparison operators`, `set operators` and `emptiness operators`.

###### Comparison operators

As mentioned above, a comparison operator is used to compose a clause that compares if a field value with a provided value by the user.
Currently, these comparison operators are supported:

* `=` (equals): checks whether the field is equal to the value on the right hand side of the operator
* `!=` (not equals): checks whether the field is not equal to the value
* `<` (less than): checks whether the field is less than the value
* `<=` (less than equal): checks whether the field is less than or equal to the value
* `>` (greater than): checks whether the field is greater than the value
* `>=` (greater than equal): checks whether the field is greater than or equal to the value
* `~` (like): checks whether the field includes any substring defined by the value
* `!~` (not like): checks whether the field does not include any substring defined by the value

###### Set operators

A set operator is used to compose a clause that checks if a field value is contained in the set of values provided by the user.
Currently, these set operators are supported:

* `IN` (include): checks whether a field value is contained in the right hand set
* `NOT IN` or `! in` (not include): check whether a field value is not contained in the right hand set

###### Emptiness operators

An emptiness operator is used to compose a clause that checks if a field value is empty or null.
Currently, these emptiness operators are supported:

* `IS EMPTY` (empty): check whether a field is empty (empty list, empty set, ...)
* `IS NOT EMPTY` (not empty): check whether a field is not empty
* `IS NULL_TOKEN` (null): check whether a field is null
* `IS NOT NULL_TOKEN` (not null): check whether a field is not null

#### Examples:

Here are some examples for both view types that you can experiment with:

#### asset-vulnerability-assessment:

* `vulnerabilityCve="CVE-2024-10039"`
* `artifactName ~ log4 and assessmentStatus = "insignificant"`
* `assessmentPriorityScore >= 5`

#### asset-vulnerable-component

* `cpe="cpe:2.3:a:redhat:keycloak:*:*:*:*:*:*:*:*"`
* `cpeVendor="keycloak" and cpeProduct="*";`
* `cpePart="*"`