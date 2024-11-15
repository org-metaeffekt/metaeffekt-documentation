# {metæffekt} Annex

## Introduction

{metæffekt} Annex - to its full extent - is an archive consisting of:
* a PDF document explaining the `subject` of interest
* an aggregation of the effective terms and licenses the `subject` is provided under
* an aggregation of specific occurrences of terms and licenses on component/part level
* an aggregation of source archives that is provided with the `subject`

The primary purpose of the {metæffekt} Annex is to address license obligation within a single archive. At least, as far
as possible, when no additional services or other option making sources available have been selected.

The `subject` of interest can be:
- a physical appliance (hardware, hardware and software)
- software (in different representations such as images, installers, archives, containers, etc.)

The intent for the annex can be:
- for documentation purposes only
- for handing-over the subject into operation (no distribution, yet gathering all relevant information)
- for distribution (sending, publishing, offer for download) the subject to one or many recipients / consumers.

For these different intents different templates and processes for composing the annex are available:
* internal terms / licenses documentation
* internal use / operation
* external distribution; covering obligations triggering on distribution

The {metæffekt} Annex can be regarded as Bill of Materials for a human user. The annex allows to include 
machine-readable BOM formats. These alone however usually cannot address all obligations.

The {metæffekt} Annex can be used to support
* `subject`-level risk identification and management
* component-level risk identification and management
* approval of the `subject` for a given purpose

## Concepts

### Associated Licenses

A component and its parts can be made available by a provider under different terms or licenses. These options enable
licensing depending on the context of use or the distribution channels in which the component is provided
or transferred. These licenses are assigned to the component at the time when obtaining the component and are referred to
as associated licenses. In contrast to associated licenses, effective licenses are the licenses under
which a component is used or distributed. The associated and effective licenses of a component may differ. The
derivation of the effective licenses is limited by the terms of the associated licenses and their options.

Remarks:
- within SPDX - for example of package element level - the declared license maps the associated license here.
- please note, that is the supply chain it is regarded advisable to differentiate declared and associated licenses.
- declared (by the creator of the package) an associated (by the consumer of the package) are regarded different concepts.

### Effective Licenses

There are several cases where a component is made available by a provider under multiple associated terms or licenses.
The effective licenses are the selected licenses relevant for the software use or distribution:
* A component is provided with a licensing option (dual or multiple licensing), where either one or the other license 
  can be used for software distribution. In this case, a single effective license must be specified.
* A component is licensed in a specific or higher version of the license. In this case, one version of the
  license can be designated as the effective license.
* For a component containing subcomponents, the respective licenses of the subcomponents shall be considered bound in 
  addition to the license of the component.
* An associated license of a component allows to sublicensing under another license. In this case, the selected license 
  is the effective license.

Remarks:
- within SPDX - for example of package element level - the concluded license maps the effective license here.
- concluded and effective licenses are regarded equivalent; we currently stick to the different terminology until 
  further notice.

### License Options

The description of Effective Licenses covers the general and most prominent cases of license options for a consumer.
The {metæffekt} Annex differentiates on an technical level immediate and secondary license options:

| Associated License - Canonical Name<br>SPDX ID / Expression                             | immediate/secondary/<br>atomic | Possible Choices as effective Licenses                                                                                                                                                                                                                                            |
|-----------------------------------------------------------------------------------------|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| BSD 3-Clause License<br>`BSD-3-Clause`                                                  | no/no/yes                      | - BSD 3-Clause License                                                                                                                                                                                                                                                            |
| BSD 3-Clause License + GNU General Public License 2.0<br>`BSD 3-Clause OR GPL-2.0-only` | yes/no/no                      | - BSD 3-Clause License<br>- GNU General Public License 2.0                                                                                                                                                                                                                          |
| GNU General Public License 2.0 (or any later version)<br>`GPL-2.0-or-later`             | yes/no/yes                     | - GNU General Public License 2.0 (or any later version)<br>- GNU General Public License 2.0<br>- GNU General Public License 3.0 (or any later version)<br>- GNU General Public License 3.0<br>- GNU General Public License >3.0 (or any later version)<br>- GNU General Public License >3.0 |
| Eclipse Public License 1.0<br>`EPL-1.0`                                                 | no/yes/yes                     | - Eclipse Public License 1.0<br>- Eclipse Public License 2.0                                                                                                                                                                                                                        |

immediate: associated licenses attribution conveys an immediate license option
secondary: associated licenses contain/enable a secondary license option
atomic: associated license has several parts; in general all the parts are effective until a selected license is concluded. 

These examples show different attributes of the example licenses and conveys the difference between immediate options 
(normally induced from the license attribution) and secondary options (usually induced from the license texts or official 
annexes or amendments to the license text).

Specifically, the EPL-1.0 has a paragraph saying

*"In addition, after a new version of the Agreement is published, Contributor may elect to distribute the Program 
(including its Contributions) under the new version."*

This is understood as secondary license option. See also the European Public Licenses (EUPLs) or the CeCill licenses for
prominent representatives of licenses with secondary license options.

The annex does not force into a choice, but reports immediate options (as these may still have to be concluded). The 
Annex does not list secondary licenses options. However, once an effective license is concluded the annex will document 
this accordingly.
