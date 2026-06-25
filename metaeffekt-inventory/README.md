# {metæffekt} Inventory

The {metæffekt} Inventory is a key concept of {metaæffekt}'s tooling approach. It can be regarded as a proprietary 
SBOM format, a data container, or a concise database covering details about software and hardware assets.

The inventory is used as data container in the automated pipelines. A such it delivers the contained data as input to
dedicated tools that can process, enrich, filter, or transform the information into other results or formats.

## Inventory Scripting Language

Since not all tools always produce the perfect, desired results the Inventors Scripting Language (ISL) is a domain 
specific language (DSL) to manipulate the contents on the inventory. At the core an ISL-object, which is wrapping
the inventory is made available and can be used to modulate the content of the inventory.

See more in [Inventory Scripting Language Reference](inventory-scripting-language/README.md).
