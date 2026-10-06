# Asset Descriptor Report Parameters

This document describes all fields and parameters that can be configured using an asset descriptor for documents and document parts in the metaeffekt reporting pipeline.

## Document Fields (`documents`)

These are the top-level fields for a document descriptor.

| Field | Type | Default | Description                                                                                                                                      | Example                    |
| :--- | :--- | :--- |:-------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------|
| `identifier` | String | (Derived from key) | The identifier of the document, used to distinguish between multiple documents when creating bookMaps in the target report directory.            | `"ae-example"`             |
| `documentParts` / `parts` | List/Map | - | Defines the document parts that make up the structure and content of this document.                                                              | `[ ... ]`                  |
| `documentType` / `type` | String | - | Representation of the document type (e.g., `VULNERABILITY_REPORT`, `LICENSE_DOCUMENTATION`). Different types trigger different validation rules. | `"VULNERABILITY_REPORT"`   |
| `params` | Map | `{}` | Document-level parameters to control structure, content, formatting, or feature toggles. See "Parameters Configuration" for the exhaustive list.   | `{ reportLanguage: "de" }` |
| `language` | String | `"en"` | The language in which the document should be produced.                                                                                           | `"de"`                     |
| `targetDocumentDir` | File path | - | The target directory for the report output. If not provided, it is usually inferred by the generation plugin.                                    | `"target/reports"`         |
| `basePath` | String | - | The base path specified in the asset descriptor for resolving relative paths of inventories and other files.                                     | `.`                        |

## Document Part Fields (`parts`)

These fields define individual structural parts of a document.

| Field | Type | Default | Description                                                                                                                 | Example                                              |
| :--- | :--- | :--- |:----------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------|
| `identifier` | String | (Derived from key) | Unique identifier for the part. Must contain only alphanumeric characters, hyphens, and underscores.                        | `"statistics-report"`                                |
| `inventoryContexts` / `inventories` | List | - | List of inventory contexts to be processed in this report part. Defines which inventory data is fed into the report.        | `[ { inventoryRef: "ae-example" } ]`                 |
| `documentPartType` / `type` | String | - | The type of the document part (e.g., `VULNERABILITY_STATISTICS_REPORT`, `ANNEX`, `INITIAL_LICENSE_DOCUMENTATION`).          | `"VULNERABILITY_STATISTICS_REPORT"`                  |
| `params` | Map | `{}` | Part-level parameters. Overwrites any conflicting parameters defined at the Document level. See "Parameters Configuration". | `{ includeAssessedColumnInOverviewTables: "false" }` |

---

## Parameters Configuration (`params`)

The following parameters can be set within the `params` section of an asset descriptor (for both `documents` and `parts`). Parameters defined at the `document` level are automatically merged with parameters defined at the `part` level (with part-level parameters taking precedence).

### Security Policy Parameters

These parameters define the security policy configurations used for vulnerability and assessment reporting.

| Parameter | Type | Default | Description | Example |
| :--- | :--- | :--- | :--- | :--- |
| `securityPolicyFile` | String | - | Path to the JSON configuration file for the central security policy. This policy defines severity mappings, advisory providers, CVSS ranges, etc. | `"security-policy-report.json"` |
| `secondarySecurityPolicyFile` | String | - | Path to an optional secondary security policy file. The configurations from this file are loaded and merge-overwritten into the primary policy. | `"secondary-policy.json"` |
| `securityPolicyActiveIds` | String | - | A comma-separated list of active IDs. These identifiers selectively activate conditional rules within the security policy. | `"cert_eu,vulnerability-report"` |

### Directory & Path Parameters

These parameters dictate where specific assets are sourced from or outputted to during the report generation.

| Parameter | Type | Default | Description | Example                     |
| :--- | :--- | :--- | :--- |:----------------------------|
| `genPath` | String | - | The relative path where SVGs and other generated assets are placed, relative to the `targetDocumentDir`. | `"resources/svg"`           |
| `referenceLicensesDir` | String | - | Path to the licenses directory of the reference inventory (used when generating differential reports). | `"../reference/licenses"`   |
| `referenceComponentsDir` | String | - | Path to the components directory of the reference inventory. | `"../reference/components"` |
| `targetLicensesDir` | String | `<targetDocumentDir>/licenses` | Directory where license texts and files will be outputted. | `"target/licenses"`         |
| `targetComponentsDir` | String | `<targetDocumentDir>/components` | Directory where component-specific files will be outputted. | `"target/components"`       |

### Table & Content Configuration

These parameters map directly to the `ReportConfigurationParameters` builder and control what information is displayed and how it is rendered.

| Parameter | Type | Default | Description | Example |
| :--- | :--- | :--- | :--- | :--- |
| `enableOpenCodeStatus` | boolean | `true` | If true, the `openCode` status for licenses is included in the license tables. | `false` |
| `includeAssessedColumnInOverviewTables` | boolean | `true` | If true, includes the "Assessed" column in the vulnerability statistics overview tables. Set to `false` to hide it and redistribute column widths. | `false` |
| `hidePriorityInformation` | boolean | `false` | If true, disables the priority label in the document across all areas, hides the priority score, and hides priority label columns in the Vulnerability list. | `true` |
| `includeInofficialOsiStatus` | boolean | `false` | Flag indicating whether to include inofficial OSI status information when detailing license characteristics. | `true` |
| `filterAdvisorySummary` | boolean | `false` | Whether to hide the periodic status "unclassified" in the vulnerability report. If `true`, the section will simply not be generated. | `true` |
| `enableSingleAssetGroups` | boolean | `false` | Controls whether the Summary Report collapses Asset Groups with only one Asset into a generic group name. Setting this to `true` disables grouping for single assets. | `true` |
| `filterVulnerabilitiesNotCoveredByArtifacts` | boolean | `false` | Whether to filter out vulnerabilities not associated with any artifacts in the inventory. Alias: `vulnerabilitiesNotCoveredByArtifacts`. | `true` |
| `inventoryAssetPrefix` | String | - | Prefix applied to inventory assets. |


### Formatting & Widths

These parameters allow you to manually adjust the column widths of specific tables in the generated DITA reports. Values represent proportional widths (similar to percentages).

| Parameter | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `reportEffectiveTableComponentColumnWidth` | int | `20` | Width of the Component column in the Effective License table. |
| `reportEffectiveTableArtifactColumnWidth` | int | `45` | Width of the Artifact column in the Effective License table. |
| `reportEffectiveTableLicenseColumnWidth` | int | `35` | Width of the License column in the Effective License table. |
| `licenseNoticeTableArtifactColumnWidth` | int | `50` | Width of the Artifact column in the License Notice table. |
| `licenseNoticeTableVersionColumnWidth` | int | `15` | Width of the Version column in the License Notice table. |
| `licenseNoticeTableLicenseColumnWidth` | int | `35` | Width of the License column in the License Notice table. |

### Report Inclusion Toggles

*Note: These are usually automatically set by the `type` of the document part (e.g., `VULNERABILITY_REPORT` sets `inventoryVulnerabilityReportEnabled` to `true`), but can be manually overridden via parameters.*

| Parameter | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `inventoryBomReportEnabled` | boolean | `false` | Enables the generation of the Inventory Bill of Materials (BOM) report. |
| `inventoryDiffReportEnabled` | boolean | `false` | Enables the generation of an inventory differential report. |
| `inventoryPomEnabled` | boolean | `false` | Enables the generation of the inventory POM. |
| `inventoryVulnerabilityReportEnabled` | boolean | `false` | Enables the detailed vulnerability report. |
| `inventoryVulnerabilityReportSummaryEnabled` | boolean | `false` | Enables the vulnerability summary report. |
| `inventoryVulnerabilityStatisticsReportEnabled` | boolean | `false` | Enables the vulnerability statistics overview report. |
| `assetBomReportEnabled` | boolean | `false` | Enables the generation of the Asset BOM report. |
| `assessmentReportEnabled` | boolean | `false` | Enables the generation of the Assessment report. |

### Fail Conditions

These parameters define whether the report generation should fail (throw an exception) when certain validation conditions are met.

| Parameter | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `failOnError` | boolean | `true` | General fail-fast flag for critical errors. |
| `failOnBanned` | boolean | `true` | Fail if banned components/licenses are found. |
| `failOnDowngrade` | boolean | `true` | Fail if a downgrade condition is detected. |
| `failOnUnknown` | boolean | `true` | Fail if unknown components/licenses are found. |
| `failOnUnknownVersion` | boolean | `true` | Fail if a component is missing a version. |
| `failOnDevelopment` | boolean | `true` | Fail if development/snapshot dependencies are included. |
| `failOnInternal` | boolean | `true` | Fail if internal violations occur. |
| `failOnUpgrade` | boolean | `true` | Fail if an unauthorized upgrade is detected. |
| `failOnMissingLicense` | boolean | `true` | Fail if an artifact is missing a license. |
| `failOnMissingLicenseFile` | boolean | `true` | Fail if a referenced license file is missing. |
| `failOnMissingNotice` | boolean | `true` | Fail if a required license notice is missing. |
| `failOnMissingComponentFiles` | boolean | `false` | Fail if required component files are missing. |
| `failOnMissingVelocityRuntimeReferences` | boolean | `true` | Fail if a Velocity template encounters an unresolved reference. |
