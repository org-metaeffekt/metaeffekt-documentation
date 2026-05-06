# Inventory Query Service

## Introduction

The Inventory Query Service is responsible for exporting the endpoints from the backend and enabling a secure-client server interaction so that the
client can send requests to the according endpoints and query the data from the database.

## Interfaces

### Endpoints

Currently, the query service exports one endpoint which is the `query` endpoint.
This endpoint allows the client to query for vulnerabilities and their related artifacts, assessments and assets and also for CPE's and their
associated vulnerabilities and artifacts.

### Request Schema

The request is sent by the client as a JSON object. It has the following fields:

| Field                | Type          | Description                                                               |
|:---------------------|:--------------|:--------------------------------------------------------------------------|
| **filterExpression** | String        | defines the expression that then filteres the result set                  | 
| **page**             | int           | describes which page is currently shown                                   |
| **pageSize**         | int           | shows the currently defined page size                                     |
| **sorting**          | List<Sorting> | a list of field names to be sorted by and their direction (`ASC`, `DESC`) |
| **viewId**           | String        | the name of the requested view                                            |

#### Token

The client has to provide a JSON Web Token (JWT) that includes authorization claims. These ensure a secure and authorized access to the database.
Those claims are a set of:

* tenantIds
* supplierIds
* audiences
* projectIds
* assets
* assetGroups
* labels

Those claims are then evaluated by the service. Only the authorization-filtered data depending on the claims is returned as a result to the client.

#### Request Example

The following JSON object represents an example request:

```json
{
  "filterExpression": "vulnerabilityCve~\"CVE-2026\"",
  "page": 1,
  "pageSize": 10,
  "viewId": "vulnerability"
}
```

In this example `"vulnerabilityCve"` is the field name of the view that holds the data for this particular request. In near future, the field names
will be adjusted to simplify the request for the user and prevent too technical field names.

Note: The `""` gets escaped when converted from user input to JSON object to correctly parse the filterExpression in the service.

### Response Schema

As a response of a clients request the server returns a JSON response object that includes the actual result with the inventory data and
additional properties for paging purposes.
The schema for the response object is as follows.

| Field             | Type             | Description                                                                                                                                                                                                           |
|:------------------|:-----------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **queryResponse** | QueryResponseDTO | represents the resulting data object, composed of a list containing the resulting rows themselves (`queryResult`) and a `details` object that contains additional detail information about some datasets for each row | 
| **size**          | long             | shows how many total elements the result has                                                                                                                                                                          | 
| **page**          | int              | shows which resulting page is currently shown                                                                                                                                                                         |
| **pageSize**      | int              | shows the currently defined page size                                                                                                                                                                                 |
| **pageCount**     | int              | shows how many total pages the result has depending on the defined page size                                                                                                                                          |

#### queryResponse

The `queryResponse` object consists of two parts: `queryResult` and `details`.
The `queryResult` is a list with the resulting row data from the requested view (`ResultDTO`).
The `details` object contains maps for each dataset for which additional data is needed. Every map in the `details` object has a unique identifier for
its keys that is used to reference the details in the `detailRefs` object in `queryResult`.

###### queryResult

The queryResult list stores elements of type `ResultDTO`. Depending on the selected view, this `ResultDTO` object is dynamically extended with
additional fields.
For example the `ResultDTO` for the `asset-vulnerable-component` view contains all fields from the `asset-vulnerability-assessment` view `ResultDTO`
and additionally includes fields for the CPE context, like `cpeProduct`, `cpeVendor` and more.

Aside from those view dependant fields, every `ResultDTO` object contains a `detailsRefs` object.
This object includes four lists, that store the `details` id references for the resulting row.

Those four lists are:

| Field                      | Example id | 
|:---------------------------|:-----------|
| **artifactDetailRef**      | A001       |
| **vulnerabilityDetailRef** | V001       |
| **assetDetailRef**         | ASE001     |
| **assessmentDetailRef**    | ASM001     |

This reference tracking is needed to prevent redundant saving of a whole details object in every `ResultDTO` that would lead to a large JSON object.
Instead, only the id reference are tracked in the list and the real detail object exists only once in the corresponding map in the `details` object.

###### Details

Currently, there are four detail objects which are represented by maps. Those are:

* `artifactDetails`
* `vulnerabilityDetails`
* `assetDetails`
* `assessmentDetails`

As mentioned before, for each of those maps separate unique ids are stored as their key and also referenced by the lists in `detailRefs` to prevent
redundancy.

##### Examples

One example of a response object is as follows:

```json
{
  "queryResponse": {
    "queryResult": [
      {
        "artifactName": "openssl",
        "assessmentContextCvssOverallScore": 4.3,
        "assessmentContextCvssSeverity": "Medium",
        "assessmentInitialCvssOverallScore": 5.3,
        "assessmentInitialCvssSeverity": "Medium",
        "assessmentPriorityLabel": "none",
        "assessmentPriorityScore": 4.1,
        "assessmentStatus": "insignificant",
        "assetAudience": null,
        "assetGroupName": "Asset Group Name",
        "assetGroupVersion": "Asset Group Version",
        "assetName": "OpenSSL",
        "assetPathInGroup": null,
        "assetSupplierId": null,
        "assetTenantId": "tenant1",
        "assetVersion": "3.0.1",
        "detailsRefs": {
          "artifactDetailRef": [
            "A001",
            "A002"
          ],
          "vulnerabilityDetailRef": [
            "V001"
          ],
          "assetDetailRef": [
            "ASE001"
          ],
          "assessmentDetailRef": [
            "ASM001"
          ]
        },
        "labelMarkerName": null,
        "vulnerabilityId": "CVE-2026-22796"
      }
    ],
    "details": {
      "artifactDetails": {
        "A001": {
          "qualifier": "openssl-3.0.1",
          "name": "openssl",
          "component": "OpenSSL",
          "version": "3.0.1",
          "license": null,
          "url": null,
          "namespace": null
        },
        "A002": {
          "qualifier": "openssl-3.0.1",
          "name": "openssl",
          "component": "OpenSSL",
          "version": "3.0.1",
          "license": null,
          "url": null,
          "namespace": null
        }
      },
      "vulnerabilityDetails": {
        "V001": {
          "providerName": "CVE",
          "providerImplementation": "CVE",
          "url": "https://nvd.nist.gov/vuln/detail/CVE-2026-22796"
        }
      },
      "assetDetails": {
        "ASE001": {
          "externalAssetId": "External Asset Id",
          "type": null,
          "architecture": "Architecture",
          "url": null,
          "comment": "Comment",
          "externalViewId": "External View ID"
        }
      },
      "assessmentDetails": {
        "ASM001": {
          "type": null,
          "initialCvssVector": "CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:N/A:L",
          "contextCvssVector": "CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:N/A:L/MPR:L",
          "assessmentRationale": "Vulnerability severity score is below insignificant threshold of 5.0.",
          "assessmentRisk": null,
          "assessmentMeasures": null,
          "assessmentBaseStatus": null
        }
      }
    }
  },
  "size": 1,
  "page": 1,
  "pageSize": 10,
  "pageCount": 1
}
```

### Inventory Query Language (IQL)

The IQL is a structured language allowing to query the inventory data using expressions and operators. 
It enables  the user to query the database with simplified textual queries rather than using technical constructs.

For more details on how to define a query visit the [Inventory Query Language](../ae-inventory-query-language/README.md).
