# {metæffekt} Documentation
Repository for aggregating and dispatching com.metaeffekt.documentation around metaeffekt tools, plugins and more.

## {metæffekt} Universe

The [{metæffekt} Universe](metaeffekt-universe/README.md) is a knowledge database covering licenses and exceptions (terms) following defined strategies and objectives. 
The underlying concept is to provide a terms database that enables a scanner to deterministically identify licenses and license exceptions in binary and source artifacts.

## {metæffekt} Kontinuum

The {metæffekt} Kontinuum combines {metæffekt} software and content into a set of modules that enable automation of license compliance management and vulnerability monitoring.
It features precise scanning, a comprehensive license database, and vulnerability assessment tools.

See for more details: [{metæffekt} Kontinuum](metaeffekt-kontinuum/README.md)

### {metæffekt} Portfolio Manager

The [{metæffekt} Portfolio Manager](metaeffekt-portfolio-manager/README.md) provides a simple and structured way to manage software-related documents—such as SBOMs and reports—while serving as a core component in an API-driven, service-oriented architecture for integration into diverse processing environments.

### {metæffekt} Vulnerability Management

The known software components are matched against known security vulnerabilities from multiple sources to generate a
[Vulnerability Assessment Dashboard (HTML)](metaeffekt-vulnerability-assessment-dashboard/README.md) and Vulnerability Report (PDF).

This process involves three key phases:
The [Vulnerability Data Mirror](metaeffekt-vulnerability-management/data-mirror/vulnerability-data-mirror.md), the [Inventory Enrichment Pipeline](metaeffekt-vulnerability-management/inventory-enrichment/inventory-enrichment-pipeline.md) 
and the Dashboard & Report generation

Find more details on [{metæffekt} Vulnerability Management](metaeffekt-vulnerability-management/vulnerability-management.md).


### {metæffekt} Asset Descriptor

Asset descriptors are a way to parameterize more complex processing steps or the generation of documents. 
An asset descriptor is a .yaml file, which defines the inputs and adds additional metadata to these inputs. 
Based on such a configuration asset-descriptor-centric plugins can evaluate and process the information.

Read more on [{metæffekt} Asset Descriptor](metaeffekt-asset-descriptor/README.md).


### {metæffekt} Annex

The {metæffekt} Annex is a structured archive that documents and manages licensing obligations for software, including license terms, 
usage conditions, and source archives to support compliance, distribution, and operational use.

More information you can find on [{metæffekt} Annex](metaeffekt-annex/README.md)
