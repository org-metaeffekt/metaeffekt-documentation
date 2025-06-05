# TMD YAML Format

## Attributes

### `canonicalName`

#### Semantics

If possible, name directly derived from the license text.

If not possible, try to find official name by searching for original website (or github, etc.) of the project/license

##### *IMPORTANT:* After changing / adding a new canonicalName add them to the canonical name list. (currently located at: "src/main/resources/ae-terms-metadata/_external/history/all-canonical-names.txt")

##### *IMPORTANT:* After changing a canonicalName make sure that everything dependent on that name is adjusted as well:
- spdxExpression
- expectedMatches
- expectedSpdxMatches
- (...)


#### Basic Conventions

Currently, the following special characters are not allowed:
* \+ (is reserved for expressions)
* , (is reserved for expressions)
* mutated vowel (these will be supported in the future)

Variants of a license include (<modifier>) in the canonical name. Example:

    canonicalName: BSD 3-Clause License (copyright holder variant)

#### Examples

INTERBASE PUBLIC LICENSE Version 1.0:

    canonicalName: Interbase Public License 1.0

#### Hints

Normal case. Keyword 'version'  or abbreviations like v1.0 are omitted.

Furthermore, the canonical name should not include the following keywords / expressions:
* license (use License with capital case)
* exception (use Exception with capital case)
* conditions (use Conditions with capital case)
* plus (often used in other schemes to express or any later version; use (or any later version) instead)

An "and" in the title of the license should be replaced with a "&".

#### Expressions - naming conventions
##### either/or expression:

* canonicalName: use "+" to connect license options

shortName:
* use "-OR-" between licenses

___

### `category`

#### Semantics

Licenses can be grouped in categories. This attribute names the category for the given license.

##### *IMPORTANT:* After changing a category make sure that everything dependent on it is adjusted as well:
- references (if reference was referring to the changed yaml or the category was unique)

#### Basic Conventions

* Category should not contain version
* Align with canonical name concerning notation
* should contain terms defining the type (such as Exception, Marker, Modifier)

#### Example

shortName: GPL-3.0 ->  

    category: GPL

#### Hints

The category is normally the short name without version; nevertheless, the category must be unique. Mixing two categories of different licenses by using the same category name may produce wrong results.

___

### `spdxIdentifier`

#### Semantics

SPDX Indentifier

#### Example

    spdxIdentifier: [ID]

#### Hints

See https://spdx.org/licenses/

---

### `shortName`

#### Semantics

Acronym of the license name. Only required in case no spdxIdentifier is provided or spdx uses '-only', '-or-later' or 'exception' suffixes and if spdx is to unspecific.
E.g. "spdxIdentifier: Intel" or if they drop the version.

#### Example

    spdxIdentifier: GPL-1.0-only  
    shortName: GPL-1.0  
    
    spdxIdentifier: GPL-2.0-or-later  
    shortName: GPL-2.0+
    
    spdxIdentifier: Classpath-exception-2.0  
    shortName: Classpath-Exception-2.0

#### Hints

A short name must not be provided if is redundant to the spdxIdentifier.

Nevertheless, the shortName must compensate notation issues we see with the SPDX conventions. E.g

* '-only' is omitted; such cases require a shortName
* '-or-later'; requires a shortName extended by '+'
* '... exception'; requires a shortName with capital Exception

---


### `type`

#### Semantics

Species the type of the metadata.

#### Basic Conventions

One of the following:

* \<empty> or 'terms': specific license terms or condition.
* 'restriction': when the metadata is adding further restrictions to the licensing
* 'exception': when the metadata specifies an exception to already existing terms; exceptions are also sorted into the _exceptions folder
* 'expression': then the metadata models an expression
* 'marker': when the metadata is rather a marker than a license
* 'modifier': when the text modifies the terms under which a license is valid
* 'reference': when the metadata is just referencing metadata.
* 'structural': when the metadata is only about mappings, masks and segmentation

#### Example

    type: exception

---

### `otherIds`

#### Semantics

Additional ids for the license from other tools and databases.

#### Example

    otherIds:
     - "scancode:atmel-firmware"

---

### `alternativeNames`

#### Semantics

Alternative names of the license.

#### Example

    alternativeNames:
    - "GPL-3.0+"
    - "GPL-3+"
    - "GPL v3+"
    - "GPL 3 or later"

#### Hints

To match a license one alternativeName is generally enough. Add further variants.

Please note, that alternative names are case-sensitive. This means that variants in case must be included in a balanced extend.

---

### `unspecific`

#### Semantics

Marks license metadata as unspecific (no version information)

#### Example

    unspecific: true

#### Hints

If multiple versions of a license exists each version has to be modelled. In addition, a undefined license meta data file need to created in the same category as fallback, when the version is available.

---

### `ignore`

#### Semantics

Marks metadata that is to be ignored when matching

#### Example

    ignore: true

#### Hints

General use for references to licenses that do not  count as associated licenses is allowed. The reference is then matched, but omitted as resolved license.

Temporary use for unresolved modelling and validation issues is allowed. In this case a verbose #FIXME: <cause> must be added to enable identification of such cases.

---

### `ignoreMatches`

#### Semantics

Lists matches of terms that can be ignored during validation of unique identification. The ignored terms can then be matched in normal matching mode (workbench).

#### Example

    ignoreMatches:
    - "GNU General Public License (undefined)"

#### Hints

Use only if multiple licenses need to be matched and our other methods (`expectedMatches`...) are not appropriate. The flag enables to keep up the validation rule that given terms are uniquely matched.

---

### `evidence`

#### Semantics

The in "matches" defined text fragments need to be matched. The `excludes` fragments must not be matched when identifying a license.

#### Example

    evidence:
        matches:
        - "ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING,
           BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF
           MERCHANTABILITY AND FITNESS FOR A PARTICULAR
           PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL"
        oneOf:
        - matches:
          - "At least this line must be identified"
        excludes:
        - "Neither the name of"

#### Hints

Matches may include wildcards marked with '\*' or '\*{ regEx }*'

* '*' matches anything regardless the length

* '\*{ regEx }*' → This wildcard  matches only the regular expression.

To match a license all evidence matches must match (except for `oneOf` matches: see below).

One exclude is enough to not match.

Usually (if possible) at least 3 matches (or grants; see below) should be provided in case a license text is available - the more specific and unique the better.

Generally we match fragments and no full sentences. E.g. '.' at the end of a match should be omitted.

When a match spans multiple sentences the match should be split. There is an exception to this rule though if the sentence is really vague or short and lacks context (like: Any warranty excluded.). In this case 2 sentences may be combined to give better context and avoid partial matches.

While `evidence` matches require all listed items to be matched, the `oneOf` 
matches enable to build groups of matches. In a group all need to match;
Only one `oneOf` group (of many potential groups) is required to complete a license match.

OneOf is usually not applied for license texts (but can be if really needed), but to match markers or
license concepts such as Public Domain. Before `oneOf` was supported
alternativeNames`` (with all included limitations) have been applied for this use case.

---

### `references`

#### Semantics

List references to other licenses. This is especially useful to archive unique license matches. Effectively the license match (only name matches - meaning only `alternativeNames`) of a referenced license is ignored.

#### Example

    references:
        "<CATEGORY-1>":
        matches:
        - "<REFERENCE TEXT>"
        "<CATEGORY-1>":
        matches:
          - "<REFERENCE TEXT>"

#### Hints

The `references` use the `category` to reference other licenses. If the `category` does not match, the reference will not have any effect.

Use this mindfully and only add `references` when you are sure that we do not want this license match to be shown.

---

### `masks`

#### Semantics

Masks text included in the license. The text is basically eliminated and not useful anymore for matching.

`Masks` apply globally. They should be managed in the license metadata where they originate from.

`Masks` are ineffective (do not apply) when the text is segmented. `Masks` only apply in a normalized form within a 
segment.

`Masks` must only be applied on text containing general information. `Masks` must not be used to eliminate license
references from a text. Use `references` instead (if we really do not want them to match.

`Masks` can be used for secondary license elimination (if the information is captured elsewhere). Check EUPL modelling.

#### Example

    masks:  
        matches:    
        - "CUPS(tm) is provided under the GNU General Public License (\"GPL\") 
           and GNU Library General Public License (\"LGPL\"), Version 2, with 
           exceptions for Apple operating systems and the OpenSSL toolkit. 
           A copy of the exceptions and licenses follow this introduction."

#### Hints

Previously modelled and in particular duplicate references can also be masked if applicable.

Only mask non-essential or license specific passages (verify before use).

---

### `combinedWith`

#### Semantics

Combines two Licenses to a new one

#### Example

In "Linking exception (GPL; variant 025)":

    combinedWith:
        "GNU General Public License 3.0":
        "GNU General Public License 3.0 (with Linking exception (GPL; variant 025))"

#### Hints

- If we detect the "Linking exception (GPL; variant 025)" and the "GNU General Public License 3.0" in one segment, then the 
resolved license would be the "GNU General Public License 3.0 (with Linking exception (GPL; variant 025)).
- Make sure to add quotes around license names containing them (e.g.: "GNU General Public License 3.0") - not including them will cause issues with combiners.

#### Conventions
- Combiners are always defined with the exception or expression. In the license text no combiners should be used to 
  combine with other licenses in an expression or exceptions. Exceptions to this rule exist, but need to be defined.

---

### `representedAs`

#### Semantics

Builds cluster of related licenses

#### Example

    representedAs: "BSD 2-Clause License"

#### Hints

Use this when dealing with license variants e.g.
"(copyright holder variant)" or "(variant 001)" and also groups like
"Permission Terms", "BSD 3-Clause License"...

---

### `displayName`

#### Semantics

Used if the canonicalName differs from the license name, to be shown in the documentation

#### Example

canonicalName: Disclaimer (variant 001) ->

    displayName: "Disclaimer (no liability; no warranty)"


canonicalName: BSD 2-Clause License (with advertizing clause; variant 001) ->

    displayName: "BSD 2-Clause License (with advertizing clause)"

#### Hints

Mostly used to cut variant tags or give further information on the license.

---

### `mappings`

#### Semantics

Maps texts. Can be applied to fix common typos or to prevent segmentation within a license file.

#### Example

    mappings:
        "As used in this License:" : "As used in this License;"

#### Hints

The mapping key and values are arguments for a regular expression replacement. This means that the key is regular expression and the mapped value can use $1 to reference groups.

The mapping is applied to all texts. This means the mapping should provide enough context to not accidentally map other parts.

Mappings are applied before segmentation and normalisation. This means that the mapping may require to compensate different formattings.

---

### `segmentation`

#### Semantics

The license scan uses several default rules (currently hardcoded) to segment a file into several parts. 
Primarily this was introduced to support segmentation of Copyright files (Debian) or "Copying" files.

Segmentation can then be modified by inserting certain markers via mappings and be reverted with the revert 
functionality.

#### Example

Add a segment using *LICENSE-SEGMENT-MARKER*. For this, insert the previous text snippet as a mapping key (from: `license-scan-segments_debug`),
and then map it to the very same text but with the `LICENSE-SEGMENT-MARKER` inserted in the wanted position.

Note that:
- both key and value have already had normalization rules (and some mappings may) have been applied.
    Thus, the required text snippets do not match plaintext, but need to match normalized in/output.
- the key is a java regex pattern.
- This mechanism is based on a simple mapping to insert a marker.
  Thus if the key matches, it will be replaced by the value.

For example:

```yaml
    mappings:
        "SEGMENT A SEGMENT B": "SEGMENT A LICENSE-SEGMENT-MARKER SEGMENT B"
```
OR
```yaml
    mappings:
        "BEGINNING OF SEGMENT B": "LICENSE-SEGMENT-MARKER BEGINNING OF SEGMENT B"
```

#### Specific Example (OpenSSH License):
We want to set a new segment before this part:
```
3)
ssh-keyscan was contributed by David Mazieres under a BSD-style
license.
```

We copy text from "input_license-scan-segments_debug" and escape all regex-relevant characters and such - here "`)`":
```yaml
mappings:
  "3\\)NEWLINE-MARKER ssh-keyscan was contributed by David Mazieres under a BSD-style": 
    " LICENSE-SEGMENT-MARKER 3) ssh-keyscan was contributed by David Mazieres under a BSD-style license."
```

#### Revert a segment:
```yaml
segmentation:
  revert: [
    { 
      matchNext:
        "Redistribution and use in source and binary forms, with or
        without modification, are permitted provided that the
        following conditions are met:"
    }
  ]
```

```yaml
segmentation:
  revert: [
    { 
      match:
        "2. Redistribution",
      matchNext:
        "This license grants a worldwide, royalty-free, perpetual and         
        irrevocable right and license to use, execute, perform,
        compile, display, copy, create derivative works of,
        distribute and sublicense the FreeType Project"
    }
  ]
```

#### Hints
- make sure to **add enough context** because the **segmentations get applied globally**
  (not enough context could be fatal!). However, the context should not be too extensive.
- Revert supports "match" and "matchNext". Leaving an attribute out means 'match anything'.
  Match is applied to the end of the current segment, matchNext is applied to the beginning of the following segment.
- The segments are re-combined on successful match.
- newlines are currently not supported - copy the license text from "input_license-scan-segments_debug" to get a
  debugged version which should work for the most part - make sure to double escape characters - one for the yaml layer
  and one for the regex layer
- use regular expressions in the normal format for mappings, not in our format (WARNING: THIS WILL CURRENTLY MAKE THE PART THAT IS BEING REPLACED BY THE REGEX PATTERN TO NOT BE MATCHED):
e.g.:
```yaml
.{0,25}
```

The matches support regular expression when in our own bracketing format.

##### Warnings:
- Make sure to delete wildcards such as "*" from matching string 
- Make sure to escape "\" (backslash character) by adding another "\" for yaml.
- Make sure to escape regex-specific characters, which may lead to funny "quadruple backslashes".

#### Characters which need to be escaped:
```yaml
single escape:
  Mapping Key: * \ "
  Mapped Value: "
```

```yaml
double escape:
  Mapping Key: ( ) { } + /
  Mapped Value:
```
The following characters *DO NOT HAVE TO BE ESCAPED*: @ 

---

### `requiresCopyright`

#### Semantics

Indicates whether the license requires copyrights in the notice

#### Example

    requiresCopyright: false/true

#### Hints

DEFAULT: false

If the license text states, that the copyright notice is required to be reproduced, included or published in the documentation, this should be set to true and if not to false.

---

### `defaultSourceCategory`

#### Semantics

States the sourceCategory, used as a default

#### Example

    defaultSourceCategory: annex

---

### `requiresAnnexNotice`

#### Semantics

Indicates whether a license notice is required for the given terms

#### Example

    requiresAnnexNotice: false

#### Hints

DEFAULT: true

---

### `requiresSourceCodeProvision`

#### Semantics

Indicates whether the license requires source code provisioning

#### Example

    requiresSourceCodeProvision: true

#### Hints

DEFAULT: false

---

### `requiresLicenseText`

#### Semantics

Indicates whether the license requires a licenseTemplate (provision of the license text in the notice)

#### Example

    requiresLicenseText: false/true

#### Hints

DEFAULT: null

If the license text states, that any text is required to be reproduced, included or published in the notice, this should be set to true and if not to false.

---

### `requiresNote`

#### Semantics

Indicates whether the license requires a note in the notice, used in connection with 'noteChecklist:'

#### Example

    requiresNote: true

#### Hints

DEFAULT: false

---

### `noteChecklist`

#### Semantics

Used if requiresNote is set to true to define what should be included in the note.

#### Example

    requiresNote: true
    noteChecklist:
    - "credit the Licensor / Original Author"

#### Hints

DEFAULT: false

---

### `articleRequired`

#### Semantics

Indicates whether the license name requires an article in order to be grammatically correct in english.

#### Example

    articleRequired: false

#### Hints

DEFAULT: true

Concerns only english.

BSD Alike, Permission Terms and Public Domain are examples for Licenses not requiring an article.

---

### `licenseTemplate`

#### Semantics

The licenseTemplate contains the license specific noticeText, used in the documentation.

#### Example

    licenseTemplate: "CONTENT"

#### Hints

If the license requires provision of the licenseText in the notice (requiresLicenseText = true), then a licenseTemplate must be added.

The content is specified in the license text.

HTML Tags need to be added:

* Surround text with:
  `<lq> ... </lq>`
  
* disclaimers are surrounded with:
  `<codeph> ... </codeph>`

  * For enumerations use a list:
  
      `<ol> <li> ... </li>`
  `<li> ... </li> </ol>`

* For other paragraphs use
  `<p> ... </p>`

The licenseTemplate may contain variables. The variables must be filled in noticeParameter.

VariableSyntax:
licenseTemplate: "CONTENT {{variableName}} CONTENT"

---

### `status`

#### Semantics

Shows the revision status of a license

#### Example

    status:
    - "initial: 25.10.21"
    - "reviewed: 25.10.21"

#### Hints

The status should contain the task and the date.

---

### `canonicalNameHistory`

#### Semantics

Previous canonicalNames of the license

#### Example

    canonicalNameHistory:
    - "Apache License 2"
    - "Apache 2.0"

#### Hints

If a license is renamed, add the old canonicalName to the history.

If two licenses are merged, add the canonicalName of the deprecated license.

canonicalNames and the canonicalNameHistory is being verified by our tests. To check if canonicalNameHistory has been added correctly run "02_IncrementalTest"

---

### `deprecated`

#### Semantics

Indicates that a license is no longer in use.

#### Example

    deprecated: true

---

### `namedEquivalence`

#### Semantics

The license represented is just a renamed copy of the referenced license.

#### Example

    namedEquivalence: MIT License

#### Hints

Used for licenses that have "only" and "any later version" iterations (that have the same license texts) - see: GNU Lesser General Public License 3.0

---

### `allowsLaterVersions`

#### Semantics

Whether the license association with this license allows for later versions of the referenced license.

#### Example

    allowsLaterVersions: true

#### Hints

Relevant/Used for licenses containing  "(or any later version)".

---

### `grants`

#### Semantics

A more detailed level of evidences.

#### Example

    grants:
        Source code delivery:
            obligations:
                Source code delivery:
                    matches:
    
                    "..."
    
        Binary delivery:
            obligations:
                Binary delivery:
                    matches:
    
                    "..."

---

### `expressiveEvidence`

#### Semantics

Indicates whether the evidence of a license is expressive (Used to escape Convention Test).

#### Example

    expressiveEvidence: true

#### Hints

Normally an evidence should contain at least 3 words. Some evidences however contain technical terms, links or foreign languages. In case the evidence has less than 3 words, due to those circumstances, expressiveEvidence can be used to confirm that the evidence is supposed to be that short.

---

### `classification`

#### Semantics

Indicates the license classification.

#### Example

    classification: permissive

#### Hints

We use the following classification:

* "commercial"
* "permissive" - matches scancodes category 'permissive'
* "copyleft"
* "limited copyleft"
* "weak copyleft"

---

### `baseTerms`

#### Semantics

Points to the original license.

#### Example

    baseTerms: GNU General Public License 2.0

#### Hints

Use this for constructs that enable license clustering.

For Example:

GNU General Public License 2.0 (with exceptions) is not an actual license but enables clustering all GPL 2.0 with 
exceptions. This construct uses baseTerms to point to the GPL 2.0 (required for Notice Engine)

---

### `alternativeShortNames`

#### Semantics

Alternative shortNames of the license.

#### Example

    alternativeShortNames:
    - "nova ratio AG"
    - "nova ratio"

#### Hints

Can be used to map

---

### `spdxExpression`

#### Semantics

Adds an Spdx Expression inn our own custom format.

This will be parsed and produce an SPDX Expression as defined by SPDX for use in exported documentation and such.

#### Example

    canonicalName: 
    -> consists of "" and ""
    combine the two canonical names with combiner "OR" and add "[]" around each License:
    spdxExpression: [Apache License 2.0] OR [BSD (undefined)]

#### Hints

Add only if separate licenses have an spdxIdentifier - do not add for undefined licenses

Work in progress

Use canonicalName of the license or exception with the combiner "WITH" in between.

Add square brackets "[]" around the licenses 

Add " " around the expression - otherwise the square brackets will be ercognized as something else!

Warning: spdxIdentifier and spdxExpression should never be in one yaml file at the same time. spdxExpression should be used as substitute if there is no spdxIdentifier!


---

### `oneOf`

#### Semantics

Type of evidence which enables one-of matching

Requires only one string or string group to be matched to completely match a marker.


#### Example

    evidence:
    oneOf:
    - matches:
        - "Display Obligation"
    - matches:
        - "distributions including binaries display the following acknowledgement"
    - matches:
        - "must display the following acknowledgement"
        - "including the acknowledgement"

#### Hints

- Evidences are grouped after each "matches:"
- Mainly used for markers, can be used for exceptions
- (At least in the debugging interface) only the first match is shown
#FIXME-CONCEPT: in the debugging interface only the first match of the evidence is shown; terrible for debugging, every match should be shown

---

## Escapes

| Character | Escaping                                                                                                             |
|---|----------------------------------------------------------------------------------------------------------------------|
| " | Double quotes can be escaped using \\". Matches and exlcude can omit double quotes as these are removed for matching |
| | |

## Keyboard shorts for special characters

```
Backslash \: <Alt/Option> + <Shift> + <7>
Pipe |: <Alt> + <7>;
```

### `expectedSpdxMatches`

#### Semantics

Defines which term are expected when the SPDX licenses are scanned with MetaScan. MetaScan may match multiple terms.
Specifically, when templates are used in the given context. The configuration prevents the SPDX synchronization tests
to report known cases. Often, also specific additions to the license text cause such issued.

#### Example

    expectedSpdxMatches:
    - "Cryptographic Autonomy License 1.0 (combined work)"
    - "Cryptographic Autonomy License 1.0"

#### Hints

This has no implication for the internal validation procedure. The purpose is only to support the SPDX license list
synchronization.

### `expectedMatches`

#### Semantics

Supports to define the match outcome for multi-licenses. **The expected licenses that are listed must be matched.**
Per default the terms itself is the only expected match. This default can be overridden by `expectedMatches`.

#### Example

    canonicalName: Node License
    [...]
    expectedMatches:
        - "Apache License 2.0"
        - "Artistic License 2.0"
        - "BSD 2-Clause License (copyright holder variant)"
        - "BSD 3-Clause License (copyright holder variant)"
        - "ISC License"
        - "MIT License"
        - "OpenSSL License"
        - "Permission Terms (variant 077)"
        - "zlib License"

#### Hints

Please use only in consideration of `ignoreMatches` and `references`. The three instruments must be used appropriately.
In the case of exceptions often `ignoreMatches` are used. This is not problematic, since the match is then compensated 
by a `combinedWith` configuration. Otherwise, `references` would be the best choice for exceptions.

Use the test `ScannerIntegrationTest` to verify the integrity of the `expectedMatches` *(Test can be found in: "src/test/java/org/metaeffekt/terms/metadata/scanner/ScannerIntegrationTest.java")*. After performing changes to fix mismatches, delete the affected files from "/target/scanner/analysis". This will make the test reevaluate those yamls and they shouldn't be shown in the test anymore, if the fixes work correctly.
