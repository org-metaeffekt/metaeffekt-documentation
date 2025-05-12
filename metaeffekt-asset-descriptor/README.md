# Asset Descriptors

Asset descriptors is a way to parameterize more complex processing steps or the generation of documents.
An asset descriptor is a .yaml file, which defined the inputs and adds additional metadata to these inputs. Based on
such a configuration asset-descriptor-centric plugins can evaluate and process the information.

## Basic Concepts

### Inventories

The primary citizens of an asset-descriptors are inventories. Many processes are inventory-centric. The current 
implementation of the asset descriptor is using inventories as input only. Conversion steps from SBOMs formats such as
SPDX and CycloneDX have to be preprocessed to be ingested by the descriptor.

## Transformations

An asset descriptor can include transformations. These initially operate on the input inventories, but may produce
further merged, combined or otherwise transformed inventories.

To use, enable or combine transformations an asset descriptor supports different transformations:

### Inline Kotlin Scripts

The asset descriptor may include compact inline kotlin scripts.

### External Kotlin Scripts

The asset descriptor may reference external kotlin script files.

### Maven Execution

The asset descriptor may invoke a maven-based processing step. This enables to include extended infrastructure. 

## Documents

An asset descriptor can describe one or more documents that are to be produced based on the asset descriptor. Each
document is defined by parts.

### Document Parts

Currently, the following parts are supported:

| Part                            | Description                                                                                   |
|---------------------------------|-----------------------------------------------------------------------------------------------|
| ANNEX                           | Produces bill of materials for inclusion in a software annex.                                 |
| VULNERABILITY_REPORT            | Produces a detailed vulnerability report content covering the identified vulnerabilities.     |
| VULNERABILITY_SUMMARY_REPORT    | Produces a detailed vulnerability summary content covering the identified vulnerabilities.    |
| VULNERABILITY_STATISTICS_REPORT | Produces a detailed vulnerability statistics content covering the identified vulnerabilities. |

The content that is produces can be integrated into document templates to produce end-user documentation.

## Examples

### Asset Descriptor for a Single-Asset Annex

Since this is a general asset descriptor it uses variables for the minimal external settings.

```
inventories:
  - "asset":
      file: "${asset.inventory.path}"
      type: INPUT
 
documents:
  "annex":
    type: ANNEX
    language: "en"
    parts:
      "annex":
        type: ANNEX
        inventories:
          - inventoryRef: "asset"
            assetName: "${asset.name}"
            assetVersion: "${asset.version}"
```

### Asset Descriptor for a Single-Asset Vulnerability Report

Since this is a general asset descriptor it uses variables for the minimal external settings.

```
inventories:
  - "asset":
      file: "${asset.inventory.path}"
      type: INPUT

documents:
  "vulnerability-report":
    type: VULNERABILITY_REPORT
    language: "en"
    params:
      "omitAssetPrefix": "true"
    parts:
      "overall-report":
        type: VULNERABILITY_STATISTICS_REPORT
        inventories:
          - inventoryRef: "asset"
            assetVersion: "${asset.version}"
            assetName: "${asset.name}"
        params:
          securityPolicyFile: "${policy.basedir}/statistics-security-policy.json"
      "report":
        type: VULNERABILITY_REPORT
        inventories:
          - inventoryRef: "asset"
            assetVersion: "${asset.version}"
            assetName: "${asset.name}"
        params:
          securityPolicyFile: "${policy.basedir}/report-security-policy.json"
```

