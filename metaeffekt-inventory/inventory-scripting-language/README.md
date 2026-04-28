# Inventory Scripting Language Reference

> [!WARNING]  
> The Inventory Scripting Language is build based on demand. In its current form it is only able to manipulate 
> artifacts (starting with artifact-analysis version 0.157.0). In the future more methods will be made available as
> required.

## The ISL Object

When using the Inventory Scripting Language the user is provided with an instance of the ISL. The ISL instance wraps
an inventory to interface with the inventory in a coordinated and controlled fashion.

## Select Operations

Select operations are directly applied on the ISL instance.

| Operation           | Effect                                  | Returned Type               |
|---------------------|-----------------------------------------|-----------------------------|
| `selectArtifacts()` | Selects all artifacts of the inventory. | ArtifactSelection instance. |

## Selection Filtering

A ArtifactSelection instance may be further filtered applying predicates. Each predicate specifies the objects that need 
to be retained in the selection. 

### Artifact Selection Predicates

| Predicate                          | Effect                                                                                  |    
|------------------------------------|-----------------------------------------------------------------------------------------|
| `idContains(String string)`          | Only artifacts remain with `Id` containing the given string.                            |
| `idMatches(String string)`           | Only artifacts remain with `Id` matching the provided regular expression.               |
| `idEquals(String string)`            | Only artifacts remain with `Id` being equal to the provided string.                     |
| `idEndsWith(String string)`        | Only artifacts remain where `Id` endsWith the provided string. Starting with version 0.158.0 |
| `suffix(String string)`              | Deprecated. Same as `idEndsWith(String string)`.                                        |
|                                    |                                                                                         |
| `pathInAssetMatches(String regexp)`  | Only artifacts remain with `Path in Asset` matching the provided regular expression.    |    
| `pathInAssetContains(String string)` | Only artifacts remain with `Path in Asset` containing the given string.                 |
| `pathInAssetEndsWith(String string)` | Only artifacts remain where `Path in Asset` ends with the given string.                 | 
|                                    |                                                                                         |
| `noVersion()`                        | Only artifacts remain with empty `Version`.                                             |
| `noLicense()`                        | Only artifacts remain with empty `License`.                                             |
| `noCopyright()`                      | Only artifacts remain with empty `Extracted Copyrights (ScanCode)`.                      |


## Modification Operations

Modification operations always operate on a Selection object.

### Artifact Selection Modifications

| Operation                         | Effect                                                                                            |
|-----------------------------------|---------------------------------------------------------------------------------------------------|
| `remove()`                        | Removes the current selection from the inventory.                                                 |    
| `setVersion(String version)`      | Sets the `Version` attribute of all artifacts in the selection to the specified `version` string.    |    
| `setComponent(String componente)` | Sets the `Component` attribute of all artifacts in the selection to the specified `component` string. |

## Script Log

Dependent on the used integration a log of the operations applied by the script is provided.
The log can be used to validate the script was applied as expected.
