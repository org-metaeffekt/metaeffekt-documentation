# Asset Descriptors

Asset descriptors are a way to parameterize more complex processing steps or the generation of documents.
An asset descriptor is a `.yaml` file, which defined the inputs and adds additional metadata to these inputs. Based on
such a configuration asset-descriptor-centric plugins can evaluate and process the information.

## Basic Concepts

### Inventories

The primary citizens of an asset descriptor are inventories. Many processes are inventory-centric. The current
implementation of the asset descriptor only uses inventories as an input. Conversion steps from SBOMs formats such as
SPDX and CycloneDX have to be preprocessed to be ingested by the descriptor.

Inventories can be declared in the top-level `inventories` section:

```yaml
inventories:
  - "reference":
      type: INPUT
      file: "../../inventories/scan-inventory.xlsx"
  - "filtered-file":
      type: COMPUTED
      file: "../../generated-inventories/integration-test-filtered-file.xlsx"
  - "filtered-inline":
      type: COMPUTED
      file: "../../generated-inventories/integration-test-filtered-inline.xlsx"
  - "transformed-with-maven":
      type: COMPUTED
      file: "../../generated-inventories/transformed-with-maven.xlsx"
```

#### Folder based inventories
Instead of a file name there is also the option to reference a complete folder as an inventory.
This can be helpful in case of i.e. merging multiple files.

Inventories can be referenced by the different types of `transformations` and inside `documents`.

#### INPUT Type

Inventories of type `INPUT` are the starting point of a transformation. They are read-only.

#### COMPUTED Type

The `COMPUTED` type marks an inventory to be a result of a transformation process or a single step of a
transformation. They can be overwritten by any part of a transformation.

## Transformations

An asset descriptor can include transformations. These initially operate on the input inventories, but may produce
further merged, combined or otherwise transformed inventories.

To use, enable or combine transformations an asset descriptor supports different transformations:

- Kotlin Scripts
- Maven Execution

### Kotlin Scripts

Use Kotlin scripts to implement custom transformations. These scripts are suffixed with `.filter.kts`.

Use a kotlin script transformation in the asset descriptor as follows:

```yaml
transformations:
  "extract-inventories":
    inputInventories:
      - "reference"
    outputInventories:
      - "filtered-file"
    params:
      "parameter_one": "value_one"
    script:
      file: "../../scripts/integration-test.filter.kts"
```

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
level section `scripts`. You should use inline scripts for shorter transformations.

##### Example

###### Transformation that refers to an inline script.

```yaml
transformations:
  "extract-inventories-inlined":
    inputInventories:
      - "reference"
    outputInventories:
      - "filtered-inline"
    params:
      "parameter_one": "value_one"
    script:
      ref: "anyway"
```

To reference an inline script use `ref` instead of `file`.

###### Declaration of an inline script

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
workspace. Their file name must end on `.filter.kts`.

#### IDE Integration

Kotlin scripts with a file name ending on `.filter.kts` are recognized by Intellij.
As a result it provides code completion, also for features provided by our own
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

To use a maven transformation several things have to be configured. Configuration includes:

- input and output inventories
- parameters needed to execute the pom
- path to `pom` file and command to execute

#### Example

```yaml
  "transform-with-maven":
    inputInventories:
      - "reference"
    outputInventories:
      - "filtered-file"
    params:
      "input.inventory": ${reference.file}
      "output.inventory": ${transformed-with-maven.file}
    maven:
      pom: "advised_enrich-inventory.xml"
      workingDir: "."
      command: "process-resources"
```

#### Referencing Inventories

Maven transformations reference an inventory as follows:

```yaml
  "transform-with-maven":
    inputInventories:
      - "reference"
    outputInventories:
      - "filtered-file"
```

#### Variable Substitution in Parameters

To enable users to reference inventory file names inside the `params` section, we implemented
a variable substitution mechanism. This mechanism allows the users to access the attributes
`file` or `folder` of a declared inventory.

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
