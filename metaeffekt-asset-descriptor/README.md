# Asset Descriptors

Asset descriptors are a way to parameterize more complex processing steps or the generation of documents.
An asset descriptor is a .yaml file, which defined the inputs and adds additional metadata to these inputs. Based on
such a configuration asset-descriptor-centric plugins can evaluate and process the information.

## Basic Concepts

### Inventories

The primary citizens of an asset-descriptors are inventories. Many processes are inventory-centric. The current
implementation of the asset descriptor only uses inventories as input. Conversion steps from SBOMs formats such as
SPDX and CycloneDX have to be preprocessed to be ingested by the descriptor.

## Transformations

An asset descriptor can include transformations. These initially operate on the input inventories, but may produce
further merged, combined or otherwise transformed inventories.

To use, enable or combine transformations an asset descriptor supports different transformations:

### Kotlin Scripts

Use Kotlin scripts to implement custom transformations. These scripts are prefixed with `filter.kts`.

#### Common Features

##### Default Imports

The environment imports selected packages by default. These must not be declared in a Kotlin script.
Use the following classes without an import:

* `org.metaeffekt.core.inventory.processor.model.Artifact`
* `org.metaeffekt.core.inventory.processor.model.Inventory`
* `org.metaeffekt.core.inventory.processor.reader.InventoryReader`
* `org.metaeffekt.core.inventory.processor.model.Constants.*`
* `com.metaeffekt.artifact.analysis.scripting.kotlin.config.inventory.InventoryUtilsKt.*`
* `java.io.File`
* `com.metaeffekt.artifact.analysis.scripting.kotlin.config.inventory.Predicates`
* `com.metaeffekt.artifact.analysis.scripting.kotlin.config.inventory.Transformations`

##### Transformations

Transformations manipulate rows of inventories, respectively `Artifacts`, according to a rule. I.e.
there are transformations to harmonize architecture names or to clean up component strings.

##### Predicates

Predicates help to filter specific artifacts out of an inventory. Use them in combination
with [collection filtering](https://kotlinlang.org/docs/collection-filtering.html).
Access predicates via the `predicates` field.

#### Inline Kotlin Scripts

The asset descriptor may include compact inline kotlin scripts. These are declared in the top
level section `scripts`. You should use inline script for shorter transformations.

##### Example

```yaml
scripts:
  "anyway": |
    import org.metaeffekt.core.inventory.processor.model.Inventory  

    val reference: Inventory = loadInventory("input.inventories.reference")  
    reference.artifacts = reference.artifacts.filter { !predicates.removeMatches(it, Regex("quarkus-run.jar"), "Id") }  
    if (params["parameter_one"] != "value_one") {  
        throw IllegalStateException("Parameter 'parameter_one' has not been passed into params map. Please check the corresponding asset-descriptor.yaml file.")  
    }  
    writeInventory(reference, "computed.inventories.filtered-inline", createParents=true)  
    println(">>> Inline script has been executed.")
```

#### External Kotlin Scripts

The asset descriptor may reference external kotlin script files. You can place Kotlin script files everywhere in your
workspace. Their file name must end on `filter.kts`.

#### IDE Integration

Kotlin scripts with a file name ending on `filter.kts` are recognized by Intellij.
As a result it provides code completion, also features provided by our own
kotlin scripting configuration.

##### Enable Integration

To enable scripting support Intellij requires `ae-kotlin-scripting-host` to be on the classpath.

1. Open `Settings | Languages & Frameworks | Kotlin | Kotlin Scripting`
2. Press `Scan Classpath`. If `ae-kotlin-scripting-host` is on the classpath an entry in the list
   of script definitions will show up.
3. Close settings.
   Intellij should now recognize your script as a kotlin script.

### Maven Execution

The asset descriptor may invoke a maven-based processing step. This enables to include extended infrastructure.

#### Referencing Inventories

#### Variable Substitution in Parameters

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
        params:
          securityPolicyFile: "${policy.basedir}/statistics-security-policy.json"
      "report":
        type: VULNERABILITY_REPORT
        inventories:
          - inventoryRef: "asset"
        params:
          securityPolicyFile: "${policy.basedir}/report-security-policy.json"
```
