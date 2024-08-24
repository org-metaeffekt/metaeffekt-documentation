# {metæffekt} Universe

## Introduction

The {metæffekt} Universe is a knowledge database covering licenses and exceptions (terms) following defined 
strategies and objectives. The underlying concept is to provide a terms database that enables a scanner to 
deterministically identify licenses and license exceptions in binary and source artifacts.

Initialized in 2016 the database has been growing in coverage and depth over the past 8 years.
Since beginning of 2023 the {metæffekt} Universe is an official product of {metæffekt}.

## Objectives

The following objectives drive our endeavors:

* **Integrity** - When a license or exception is added to the {metæffekt} Universe it must be uniquely
  matched. That means that the {metæffekt} License Scanner using the universe uniquely identifies 
  the license or exception. Similar license must explicitly be differentiated within the database.
* **Consistency** - While embracing change the {metæffekt} Universe applies a variety of rules and conventions 
  on different aspects. Included but not limited to:
  * Naming and identification - The main identifier for a license or exception is its canonical name; the name is
    intended to be expressive and follows conventions to ensure a harmonized the naming and identification scheme over 
    the database.
  * Mapping to other systems - Mapping to other license / exception systems (such as SPDX License / Exception List or 
    ScanCode) is based on semantic equivalence. In case the counterpart license / exception deviates different 
    approaches apply. In any case, semantic equivalence is never sacrificed.
* **Coverage of SPDX Licenses and Exceptions** - The SPDX Licenses and Exceptions List are continuously synchronized
  into the {metæffekt} Universe. We actively participate in the SPDX-Legal Call adding information and insights to the
  different SPDX issues. See [SPDX License List Issues](https://github.com/spdx/license-list-XML/issues?page=1&q=is%3Aissue+is%3Aopen).
  Please also check the [{metæffekt} Universe Github Repository](https://github.com/org-metaeffekt/metaeffekt-universe) 
  for details and current mapping / coverage.
* **Coverage of ScanCode** - The licenses and rules of ScanCode are regularly synchronized with the universe. We attempt
  to cover all licenses in ScanCode, as long as these align with the universe paradigms and objectives. Coverage of 
  ScanCode has several facets:
  * we match the representative license text using the ScanCode scanner and review deviations. 
  * we associate licenses with ScanCode ids in case we see an semantic equivalence relationship.
  * we try to understand deviations and ambiguities within the mapping / missing mappings.
* **Coverage of OSI Licenses** - We cover approved OSI licenses as well as licenses with unofficial OSI status that we 
  collected from different OSI-centric materials such as wiki content of mailing lists. 
  Compare [{metæffekt} Universe Github Repository - Further Information](https://github.com/org-metaeffekt/metaeffekt-universe#further-information).
* **Coverage of Licenses / Exceptions found in the Wild** - Any public available license or exception that is identified 
  in customer projects is isolated and added to the {metæffekt} Universe. Proprietary and confidential licenses are 
  added to the universe as customer-local extensions.
* **Control Deviations & Inconsistencies with external Systems** - Everywhere where inconsistencies are detected with 
  mappings to external system these inconsistencies are collected and regularly reviewed.
* **Understanding Risk** - Based on the {metæffekt} Universe it must be possible to identify and highlight risks induced 
  by the terms and conditions of the covered licenses.
* **Automated Documentation** - The models in the database must enable to generated automated notices for documentation.

## Additional Concepts

In the following selected concepts are outlined. 

### Licenses and License Associations

We regard that SPDX is mixing several concepts in the SPDX License List. We differentiate between a license identifier
and a license association. A prominent example are the GNU GPL Licenses. SPDX uses GPL-3.0-only and GPL-3.0-or-later.
The {metæffekt} Universe identifies the licenses (there is only one) as GNU General Public License 3.0. When the license
is applied the copyright owners associate a license. They can associate with license as is GNU General Public License 
3.0 which would be a GPL-3.0-only association in SPDX jargon, or they can associate 
GNU General Public License 3.0 (or any later version) equivalent to SPDX's GPL-3.0-or-later.

So the universe works with plain license names and modifiers. See also next section.

### Modifiers
Licenses may have several modifiers or represent a variation. Currently, SPDX - in our opinion - struggles with that 
quite a bit. See also [SPDX Change Request - Modifiers](https://github.com/spdx/change-proposal/blob/main/proposals/Modifiers.md). 

In the universe we simply enumerate modifiers using the following the pattern 

    <canonical license name> (<modifier>[; <modifier>]*) 

Please note that multiple modifiers are possible enabling combinatorics instead of introducing
a new license identifier for each combination.

### Markers

Not all aspects can be covered in canonical names and modifiers. To further extend license identification into risk
assessment the {metæffekt} Universe features so-called markers. These markers are basically keyword, sentence fragments 
or regular expressions that highlight certain aspects of a text. Markers are a major instrument to qualify licenses
in the universe. E.g. markers may highlight statements on patents, crypto, or import/export regulation.

### Groups

Terms can be grouped in categories as well as by a representative license or base license. Withing the different
grouping features different processing rules apply.

### Variables

Terms may use placeholder or variables. Based on the identification the variables may be extracted and 
used to replicate the exact match.

### Notice Generation

Further attributes in the terms model enable to control the generation of notices for documentation purposes.

### Undefined License Identification

In cases a license identification is imprecise, in particular the concrete license or license version cannot be 
matched, the {metæffekt} Universe identifies the license as undefined in the form:

    <canonical license name> (undefined)

Many tools derive a default version of the license in such cases.

### Canonical Name History

To anticipate change the database keeps track of name changes. As we proceed we may learn about further variations
of licenses and may want to revise names. To enable this a canonical name history is maintained. This way former
identifications can be mapped precisely.

## Plans
As part of the {metæffekt} Kontinuum release we plan to open source a significant part of the {metæffekt} Universe. We 
would like to provide an Open {metæffekt} Universe covering publicly available licenses and exceptions.
