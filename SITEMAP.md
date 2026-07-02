# Documentation Sitemap

- [{metæffekt} Documentation](README.md)
- [Howto Document](HOWTO-DOCUMENT-README.md)
- [Important Links](important-links.md)

## [{metæffekt} Annex](metaeffekt-annex/README.md)

- Docs
  - [Notice Parameter](metaeffekt-annex/docs/notice-parameter/README.md)

## [Asset Descriptors](metaeffekt-asset-descriptor/README.md)


## [{metæffekt} Inventory](metaeffekt-inventory/README.md)

- [Inventory Scripting Language Reference](metaeffekt-inventory/inventory-scripting-language/README.md)

## [{metæffekt} Kontinuum](metaeffekt-kontinuum/README.md)


## [{metæffekt} Portfolio Manager](metaeffekt-portfolio-manager/README.md)


## [{metæffekt} Universe](metaeffekt-universe/README.md)

- Docs
  - [{metæffekt} Universe - CI/CD Integration](metaeffekt-universe/docs/ci-integration.md)
  - [TMD YAML Format](metaeffekt-universe/docs/yaml-format.md)

## [{metæffekt} Vulnerability Management](metaeffekt-vulnerability-management/README.md)

- [Changelog](metaeffekt-vulnerability-management/changelog/README.md)
  - [Changelog 0.110.0 - 0.117.0](metaeffekt-vulnerability-management/changelog/0.110.0-0.117.0.md)
- [Vulnerability Data Mirror](metaeffekt-vulnerability-management/data-mirror/README.md)
  - [Vulnerability Mirror Data Sources](metaeffekt-vulnerability-management/data-mirror/data-sources/README.md)
    - [CSAF (Common Security Advisory Framework)](metaeffekt-vulnerability-management/data-mirror/data-sources/csaf/README.md)
      - [Why no Log4J in CSAF?](metaeffekt-vulnerability-management/data-mirror/data-sources/csaf/missinglog4j.md)
    - [EOL (endoflife.date)](metaeffekt-vulnerability-management/data-mirror/data-sources/eol/README.md)
      - [EOL Data](metaeffekt-vulnerability-management/data-mirror/data-sources/eol/eol-data-specific-cycles.md)
      - [EOL Date Data](metaeffekt-vulnerability-management/data-mirror/data-sources/eol/understanding-data.md)
    - [MSRC (Microsoft Security Response Center)](metaeffekt-vulnerability-management/data-mirror/data-sources/msrc/README.md)
      - [Finding Microsoft Product Ids](metaeffekt-vulnerability-management/data-mirror/data-sources/msrc/finding-microsoft-product-ids.md)
      - [Microsoft Product Patches](metaeffekt-vulnerability-management/data-mirror/data-sources/msrc/msrc-product-kbs.md)
      - [KB Research](metaeffekt-vulnerability-management/data-mirror/data-sources/msrc/understanding-data.md)
    - [Open Source Vulnerabilities (OSV)](metaeffekt-vulnerability-management/data-mirror/data-sources/osv/README.md)
      - [AE-1477: OSV PURL-Based Vulnerability Matching Refactor / Fixed Versions tracking](metaeffekt-vulnerability-management/data-mirror/data-sources/osv/AE-1477.md)
  - [Downloaders](metaeffekt-vulnerability-management/data-mirror/downloaders/README.md)
    - [Downloaders: POM Configuration](metaeffekt-vulnerability-management/data-mirror/downloaders/downloaders-pom.md)
    - [All Downloaders](metaeffekt-vulnerability-management/data-mirror/downloaders/downloaders.md)
  - [Indexers](metaeffekt-vulnerability-management/data-mirror/indexers/README.md)
    - Diagrams
      - [Individual Index Diagrams](metaeffekt-vulnerability-management/data-mirror/indexers/diagrams/individual-index-diagrams.md)
    - [Index Structure](metaeffekt-vulnerability-management/data-mirror/indexers/index-examples.md)
    - [Indexers: POM Configuration](metaeffekt-vulnerability-management/data-mirror/indexers/index-pom.md)
    - [All Indexers](metaeffekt-vulnerability-management/data-mirror/indexers/index.md)
- Documentation Generator
  - [Building the Vulnerability Documentation](metaeffekt-vulnerability-management/documentation-generator/how-to-doc.md)
- [Inventory Enrichment Pipeline](metaeffekt-vulnerability-management/inventory-enrichment/README.md)
  - [Inventory Enrichment: POM Configuration](metaeffekt-vulnerability-management/inventory-enrichment/inventory-enrichment-pom.md)
  - [Inventory Enrichment Steps](metaeffekt-vulnerability-management/inventory-enrichment/inventory-enrichment-steps.md)
- [Reports](metaeffekt-vulnerability-management/reports/README.md)
  - [{metæffekt} Inventory Overview Report](metaeffekt-vulnerability-management/reports/inventory-overview-report/README.md)
    - [Inventory Overview Report: Notifications](metaeffekt-vulnerability-management/reports/inventory-overview-report/notifications.md)
  - [{metæffekt} Vulnerability Assessment Dashboard](metaeffekt-vulnerability-management/reports/vulnerability-assessment-dashboard/README.md)
    - Concepts
      - [Concept: Due Review](metaeffekt-vulnerability-management/reports/vulnerability-assessment-dashboard/concepts/due-review.md)
    - [Assessment Management Service](metaeffekt-vulnerability-management/reports/vulnerability-assessment-dashboard/assessment-management-service.md)
    - [Assessment Dashboard: Changes from Version 3](metaeffekt-vulnerability-management/reports/vulnerability-assessment-dashboard/changes-from-version-3.md)
    - [Assessment Dashboard: Generation](metaeffekt-vulnerability-management/reports/vulnerability-assessment-dashboard/dashboard-creation.md)
    - [Vulnerability Assessment Dashboard Detail Levels](metaeffekt-vulnerability-management/reports/vulnerability-assessment-dashboard/vad-detail-levels.md)
- [Technical Specifications](metaeffekt-vulnerability-management/technical-specifications/README.md)
  - Algorithms Calculations
    - Cvss
      - [CVSS 4.0 Calculation](metaeffekt-vulnerability-management/technical-specifications/algorithms-calculations/cvss/cvss-4.0-calculation.md)
      - [Updating CVSS Entities](metaeffekt-vulnerability-management/technical-specifications/algorithms-calculations/cvss/updating-cvss-entities.md)
    - Priority Score
      - [Priority Score](metaeffekt-vulnerability-management/technical-specifications/algorithms-calculations/priority-score/priority-score-system.md)
    - Security Policy
      - [CSP Loader](metaeffekt-vulnerability-management/technical-specifications/algorithms-calculations/security-policy/central-security-policy-loader.md)
      - [Central Security Policy](metaeffekt-vulnerability-management/technical-specifications/algorithms-calculations/security-policy/central-security-policy.md)
    - [Inventory Merger (flatten vulnerabilities)](metaeffekt-vulnerability-management/technical-specifications/algorithms-calculations/inventory-merger.md)
    - [Introduction](metaeffekt-vulnerability-management/technical-specifications/algorithms-calculations/parsing-effective-cpe.md)
    - [Version Comparator](metaeffekt-vulnerability-management/technical-specifications/algorithms-calculations/version-comparator.md)
  - Formats
    - [Vulnerability Assessment Format](metaeffekt-vulnerability-management/technical-specifications/formats/assessment/README.md)
      - [Application of Vulnerability Assessments](metaeffekt-vulnerability-management/technical-specifications/formats/assessment/vulnerability-assessment-application.md)
      - [Vulnerability Assessment (Version 1.0, Generation 3.x)](metaeffekt-vulnerability-management/technical-specifications/formats/assessment/vulnerability-assessment-file-gen-3.md)
      - [Vulnerability Assessment (Version 2.0, Generation 4.x)](metaeffekt-vulnerability-management/technical-specifications/formats/assessment/vulnerability-assessment-file-gen-4-2.0.md)
      - [Vulnerability Assessment (Version 2.1, Generation 4.x)](metaeffekt-vulnerability-management/technical-specifications/formats/assessment/vulnerability-assessment-file-gen-4-2.1.md)
      - [Vulnerability Assessment (Version 2.2)](metaeffekt-vulnerability-management/technical-specifications/formats/assessment/vulnerability-assessment-file-gen-4-2.2.md)
      - [Assessment Format Upgrade Tool](metaeffekt-vulnerability-management/technical-specifications/formats/assessment/vulnerability-assessment-upgrade-plugin.md)
    - [Content Identifiers](metaeffekt-vulnerability-management/technical-specifications/formats/content-identifiers.md)
    - [Correlation Transformer](metaeffekt-vulnerability-management/technical-specifications/formats/correlation-transformer.md)
    - [Tracking of Fixed Software Configurations](metaeffekt-vulnerability-management/technical-specifications/formats/fixed-software-versions.md)
    - [PURL Derivation](metaeffekt-vulnerability-management/technical-specifications/formats/purl-derivation.md)
    - [Package URLs](metaeffekt-vulnerability-management/technical-specifications/formats/purl.md)
    - [Vulnerability Filter](metaeffekt-vulnerability-management/technical-specifications/formats/vulnerability-filter-format.md)
    - [Vulnerability Keywords](metaeffekt-vulnerability-management/technical-specifications/formats/vulnerability-keywords.md)
    - [Inventory Vulnerability Status differ](metaeffekt-vulnerability-management/technical-specifications/formats/vulnerability-status-differ.md)
