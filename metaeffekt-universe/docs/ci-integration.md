# CI/CD Integration

The {metæffekt} Universe data can be used for integration with existing tools. In particular,
it is possible to export a set of yaml files that provide details on licenses and supports a simple
categorization of licenses.

## Terms YAML

The terms.yaml file simply list the terms with relevant attributes. An example extract:

    - terms:
        [...]
        - canonicalName: "Apache License 2.0"
          shortName: "Apache-2.0"
          type: "terms"
          classification: "permissive"
          spdxExpression: "Apache-2.0"
          scanCodeId: "apache-2.0"
          spdxLink: "https://spdx.org/licenses/Apache-2.0.html"
          scanCodeLink: "https://github.com/nexB/scancode-toolkit/blob/develop/src/licensedcode/data/licenses/apache-2.0.LICENSE"
          alternativeNames:
            - "Apache software license, Version 2.0"
            - "Apache License, ASL Version 2.0"
            - "Apache Sofware License, Version 2.0"
            - "Apache Software License,Version 2.0"
            - "Apache Software License, Verision 2.0"
            - "Apache Software License, Verion 2.0"
            - "Apache Software License, Ve rsion 2.0"
            - "Apache Software License 2.0"
            - "Apache Software Licence, Version 2.0"
            - "Apache Software License, Versino 2.0"
            - "Apache Public License, Version 2"
            - "Apache License (VERSION 2.0)"
            - "Apache Licence, version 2.0"
            - "Apache2 License"
            - "Apache-2.0 License"
            [...]
            - "http://opensource.org/licenses/apache2.0.php"
            - "Apache (Software) License, version 2.0"
            - "http://xml.apache.org/xerces2-j/"
            - "Apache License, = = Version 2.0"
            - "Apache (ASL) 2.0"
            - "Apache Software License v2"
            - "www.apache.org/licenses/LICENSE-2.0"
            - "Apache-2.0"
            - "LicenseRef-scancode-apache-2.0"
            - "LicenseRef-ae-Apache-2.0"
    [...]

Please note the SPDX and ScanCode identifiers, type and classification attributes.

## Categories YAML

Based on this extensive terms.yaml. Different validation sets for different use or rather business
cases can be created. Within the identified categories the canonical license names can be listed:

    categories:
        permittedWithMinorObligations:
            - ...
        permittedWithModerateObligations:
            - ...
        permittedWithMajorObligations:
            - ...
        proprietary:
            - ...
        prohibited:
            - ...
        uncategorized:
            - ...
        ignored:
            - ...

In a for example commercial setting the Apache License 2.0 from above may be categorized
as follows depending on the business-case-specific license assessment and assessment policy of the 
organization:

    categories:
        permittedWithMinorObligations:
            - Apache License 2.0
            - ...
        permittedWithModerateObligations:
            - ...
        permittedWithMajorObligations:
            - ...
        proprietary:
            - ...
        prohibited:
            - ...
        uncategorized:
            - ...
        ignored:
            - ...

The proposed categories here have the following background on operation aspects:

| Category                             | Description                                                                                                                                                                                                                                                                                                      | Triggered Activities                                                                                                                                                                                      |
|--------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **permittedWithMinorObligations**    | Licenses or generally terms in this category have no to only minor obligations, which are either easy to fulfill or implicitly fulfilled by the organizations' processes and procedures.                                                                                                                         | Detecting a license of this category does not trigger specific activities.                                                                                                                                |
| **permittedWithModerateObligations** | Terms in this category require additional measures to fulfill the obligations. Yet the obligations are moderate in terms of effort and can be fulfilled.                                                                                                                                                         | Detecting a license of this category would trigger concurrent non-blocking activities.                                                                                                                    |
| **permittedWithMajorObligations**    | Terms in this category require additional measures to fulfill the obligations. In contrast to permittedWithModerateObligations fulfilling the obligations may consume significant resources and may cause additional efforts and costs in downstream processes and procedures.                                   | Detecting a license of this category would trigger blocking activities to validate the obligation can be effectively fulfilled.                                                                           |
| **proprietary**                      | Terms identified to match this category require assessment whether the specific or commercial prerequisites are covered. When for example identifying a commercial license that requires a subscription for the applied usage scenarios, it must be verified that the commercial asset is sufficiently licensed. | Detecting a license of this category would trigger information gathering regarding proprietary / commercial licenses in the organization to validate whether the conditions can be effectively fulfilled. |
| **prohibited**                       | Terms identified to match this category have been excluded due to conditions, restrictions or obligations that cannot be fulfilled in the given use / business case.                                                                                                                                             | Detecting a license of this category would trigger immediate analysis of the identification and activities to eliminate the software component under given license.                                       |
| **uncategorized**                    | Terms identified to match this category have not (yet) been qualified for one of the other categories.                                                                                                                                                                                                           | Detecting a license of this category would trigger immediate assessment and categorization of the license. Then the activity of the resulting category apply.                                             |
| **ignored**                          | Terms identified to match this category have been found to not be relevant in the current case. This category is initially empty and is grown in operation. This category may for example cover licenses defined by the organization itself.                                                                     | Detecting a license of this category would trigger no immediate reaction. The ignored category is nevertheless constantly reviewed.                                                                       |
