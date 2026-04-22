# Query Service

## Interfaces

### Endpoints

Currently, the query service exports one endpoint which is the `query endpoint.
This endpoint allows the client to query for vulnerabilities and their relating artifacts, assessments and assets and also for CPE's and their
relating vulnerabilities and artifacts.

### Request Schema

The request is sent by the client via a UI as a JSON object.
It has the following fields:

| Field                | Type          | Description                                                               |
|:---------------------|:--------------|:--------------------------------------------------------------------------|
| **filterExpression** | String        | defines the expression that then filteres the result set                  | 
| **page**             | int           | describes which page is currently shown                                   |
| **pageSize**         | int           | shows the currently defined page size                                     |
| **sorting**          | List<Sorting> | a list of field names to be sorted by and their direction (`ASC`, `DESC`) |
| **viewId**           | String        | the name of the requested view                                            |

#### Token

The client has to provide a JWT token that includes claims. Those claims are a set of:

* tenants
* audiences
* projects
* labels
* components

Those claims are then evaluated by the backend and only the filtered data depending on the claims is returned as a result to the client.

#### Request example

The following JSON object represents an example request:

```json
{
  "filterExpression": "vulnerabilityCve~\"CVE-2026\"",
  "page": 1,
  "pageSize": 10,
  "viewId": "vulnerability"
}
```

Note: The `""` get escaped when converted from user input to JSON object to correctly parse the filterExpression in the backend.

### Response Schema

As a response of a clients request the server returns a JSON response object that includes the actual result object with the inventory data and
additional properties for paging purposes.
The schema for the response object is as follows.

| Field                | Type             | Description                                                                                                                                                                                                           |
|:---------------------|:-----------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **queryResponseDTO** | QueryResponseDTO | represents the resulting data object, composed of a list containing the resulting rows themselfes (`queryResult`) and a `details` object that contains additional detail information about some entities for each row | 
| **size**             | long             | shows how many total elements the result has                                                                                                                                                                          | 
| **page**             | int              | shows which resulting page is currently shown                                                                                                                                                                         |
| **pageSize**         | int              | shows the currently defined page size                                                                                                                                                                                 |
| **pageCount**        | int              | shows how many total pages the result has depending on the defined page size                                                                                                                                          |

#### queryResponseDTO

The `queryResponseDTO` object consists of two parts: `queryResult` and `details`.
The `queryResult` is a list with the resulting row data from the requested view (`ResultDTO`).
The `details` object contains maps for each entity for which additional data is needed. Every map in the `details` object has a unique identifier for
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

###### details

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
  "queryResponseDTO": {
    "queryResult": [
      {
        "artifactName": "artifact-x",
        "assessmentContextCvssOverallScore": 3.3,
        "assessmentContextCvssSeverity": "Medium",
        "assessmentInitialCvssOverallScore": 6.3,
        "assessmentInitialCvssSeverity": "Medium",
        "assessmentPriorityLabel": "none",
        "assessmentPriorityScore": 4.1,
        "assessmentStatus": "insignificant",
        "assetAudience": null,
        "assetName": "ArtifactX",
        "assetPath": null,
        "assetSupplier": null,
        "assetVersion": "1.3.1",
        "detailsRefs": {
          "artifactDetailRef": [
            "A001"
          ],
          "vulnerabilityDetailRef": [
            "V003"
          ],
          "assetDetailRef": [
            "ASE001"
          ],
          "assessmentDetailRef": [
            "ASM001"
          ]
        },
        "labelMarkerName": null,
        "project": null,
        "projectVersion": "Project Version",
        "vulnerability": "CVE-2026-22796"
      }
    ],
    "details": {
      "artifactDetails": {
        "A001": {
          "name": "artifact-x",
          "component": "Artifact-X",
          "groupId": null,
          "version": "1.3.1",
          "license": null,
          "url": null
        }
      },
      "vulnerabilityDetails": {
        "V003": {
          "providerName": "CVE",
          "providerImplementation": "CVE",
          "url": "https://nvd.nist.gov/vuln/detail/CVE-2026-22796"
        }
      },
      "assetDetails": {
        "ASE001": {
          "externalAssetId": "External Asset Id",
          "externalProjectId": "External Project Id",
          "type": null,
          "assessment": null,
          "architecture": "Architecture",
          "url": null,
          "comment": "Comment"
        }
      },
      "assessmentDetails": {
        "ASM001": {
          "type": "project",
          "initialCvssVector": "CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:N/A:L",
          "contextCvssVector": "CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:N/A:L/MPR:L",
          "assessmentRationale": "Vulnerability severity score is below insignificant threshold of 7.0.",
          "assessmentRisk": null,
          "assessmentMeasures": null,
          "assessmentBaseStatus": null
        }
      }
    }
  },
  "size": 2,
  "page": 1,
  "pageSize": 10,
  "pageCount": 1
}
```

### IQL

The `IQL` (Inventory Query Language) is a custom language that allows querying the inventory data using expressions and operators.
It is case-insensitive and whitespaces are skipped.

For more details on how to define a query visit the [Query Language Readme](../ae-inventory-query-language/README.md)