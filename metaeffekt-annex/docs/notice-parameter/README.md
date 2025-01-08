# Notice Parameter

For the generation of user-centric software documentation component-centric license notices are generated. These license
notices convey the associated licenses, the effective licenses and - in case required or supportive - partially the 
structure of a component. Based on past practices writing license notices manually in the past a generator for the 
license notices has been developed. This generator however demands structured input and concluded knowledge on component
level. This structure input for the generation of component-centric license notices are the *Notice Parameters*.

## Structure

The notice parameter is written in YAML and always starts with a `component` definition. This definition describes the 
top-level or primary constituent. Each component definition may include the following attributes:

- `name`: the name of the component
- `associatedLicenses`: list of licenses regarded associated to the component by the component supplier.
- `effectiveLicenses`: list of effective licenses concluded by the distributor.
- `copyrights`: list of copyrights associated with the component and the associated licenses.
- `note`: additional remarks to include in the license notice.
- `missingCopyrights`: boolean indicating with ´true´ that no copyright is conveyed; defaults to `false`.
- `incompatibleWithSecondaryLicenses`: boolean indicator to convey whether secondary licenses have been disallowed by 
  the supplier. Some licenses allow secondary licenses to be used. In some cases this options can be limited by the 
  supplier.
- `componentStatus`: will not be included in the generated notice. Allows to document the maturity of the component
  description or may indicate issues when notice parameter was automatically derived.
- `variables`: The generator uses templates for generating notices. These templates themselves may use variables or
  placeholders that require to be provided. This element provides a list of values for the variables used in the 
  template.

The `component` definition may be followed by further details on component parts introduced by further 'subcomponents'.
The definition of a subcomponent uses the same attributes as the component definition above.

The additional attribute `unassignedCopyrights` may be used to convey further copyrights that cannot be clearly assigned
to a license.

# Further Resources

A JSON schema file applicable to notice parameter YAML files can be found here:
[notice-parameters.json](https://github.com/org-metaeffekt/metaeffekt-artifact-analysis/blob/main/modules/ae-notice-engine/src/main/resources/json-schema/notice-parameters.json).

Examples of notice parameter YAML can be found in the [notice engine test resources](https://github.com/org-metaeffekt/metaeffekt-artifact-analysis/tree/main/modules/ae-notice-engine/src/test/resources/notice-template-tests).

## Examples

### Pure Apache License 2.0 licensed component

    component:
      name: XYZ
    associatedLicenses:
    - Apache License 2.0

This is the most minimal notice parameter possible specifying only a name and one associatedLicense.

The notice generated from it looks as follows (adjusted for markdown):

>The herein covered software distribution contains <b>XYZ</b>.<br>In the present version, XYZ is restricted to the 
terms of the Apache License 2.0.

The Apache License 2.0 does not require to document the copyright on unmodifed distribution. Therefore, the notice is 
regarded complete.

### Spring Framework `spring-core-5.3.39.jar`

A notice parameter is a data structure based on the simple concepts conveyed by the following YAML example based on
Spring Framework's `spring-core-5.3.39.jar` java archive.

    component:
      name: Spring Framework
      associatedLicenses:
        - Apache License 2.0
    subcomponents:
      - name: ASM
        associatedLicenses:
          - BSD 3-Clause License (copyright variant)
        copyrights:
          - Copyright (c) 2000-2011 INRIA, France Telecom All rights reserved.
        variables:
            variable3rdClause: "the name of the copyright holders nor the names of its contributors"
            copyrightHolderNoWarranty: "THE COPYRIGHT HOLDERS AND CONTRIBUTORS"
            copyrightHolderNoLiability: "THE COPYRIGHT OWNER OR CONTRIBUTORS"

This notice parameter generates the following notice (adjusted for markdown):

> The herein covered software distribution contains <b>Spring Framework</b> with subcomponents.
In the present version, Spring Framework and the included subcomponents are restricted to the terms of the Apache 
License 2.0, and the BSD 3-Clause License (copyright variant).</p><p>The <b>Spring Framework</b> specific parts are 
licensed under the terms of the Apache License 2.0.
<br>
<br>
> The subcomponent <b>ASM</b> is licensed under the terms of the BSD 3-Clause License (copyright variant).
<br>
<br>
The BSD 3-Clause License (copyright variant) requires to reproduce the component specific copyright:</p>
Copyright (c) 2000-2011 INRIA, France Telecom All rights reserved.
<br>
<br>
Further, the BSD 3-Clause License (copyright variant) requires to reproduce the license conditions and disclaimer:
<br>
<br>
Redistribution and use in source and binary forms, with or without modification, are permitted provided that the
following conditions are met: <ol><li>Redistributions of source code must retain the above copyright notice, this list
of conditions and the following disclaimer.</li><li>Redistributions in binary form must reproduce the above copyright
notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with
the distribution.</li><li>Neither the name of the copyright holders nor the names of its contributors may be used to
endorse or promote products derived from this software without specific prior written permission.</li></ol>
`THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED
WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR
PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT,
INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE
GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF
LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF
THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.`

Please note that the generated notice has parts describing the license situation and parts, which are dedicated to the
individual license. Different licenses may have different demands to the notice parameter. In particular some licenses
demand the copyrights to be available or - depending on the template - a set of variables. Whether an attribute of
a component definition is mandatory is defined by the context and the demands of the individual licenses. Complete 
validation of the component patterns is therefore only possible in combination with the license database.

### License Option / Effective Licenses

The examples in this section are based on the fact, that a component is distributed under a license option. The
user / distributor may choose either one of the licenses.

    component:
      name: ABC
      associatedLicenses:
      - Apache License 2.0 + BSD 3-Clause License
      effectiveLicenses:
        - Apache License 2.0
        copyrights:
        - Copyright (c) 2019-2025, the original authors. All rights reserved.

The above notice parameters produces:

>The herein covered software distribution contains <b>ABC</b>.</p><p>In the present version, ABC is restricted to
the terms of either the Apache License 2.0 or the BSD 3-Clause License.</p><p>For the parts restricted to either the
Apache License 2.0 or the BSD 3-Clause License, the Apache License 2.0 has been selected for the software
distribution.</p><p>Subsequently, the terms and conditions of the Apache License 2.0 apply for <b>ABC</b>.</p>

Here the effectiveLicenses was determined to the Apache License 2.0.

Using BSD 3-Clause License as effective license:

    component:
      name: ABC
      associatedLicenses:
      - Apache License 2.0 + BSD 3-Clause License
      effectiveLicenses:
        - BSD 3-Clause License
        copyrights:
        - Copyright (c) 2019-2025, the original authors. All rights reserved.

Produces notice (adjusted for markdown):

>The herein covered software distribution contains <b>ABC</b>.</p><p>In the present version, ABC is restricted to
the terms of either the Apache License 2.0 or the BSD 3-Clause License.</p><p>For the parts restricted to either the
Apache License 2.0 or the BSD 3-Clause License, the BSD 3-Clause License has been selected for the software
distribution.</p><p>Subsequently, the terms and conditions of the BSD 3-Clause License apply for <b>ABC</b>.</p><p>The
BSD 3-Clause License requires to reproduce the component specific copyright:</p><lq><lines><line>Copyright (c)
2019-2025, the original authors. All rights reserved.</line></lines></lq><p>Further, the BSD 3-Clause License requires
to reproduce the license conditions and disclaimer:</p><lq>Redistribution and use in source and binary forms, with or
without modification, are permitted provided that the following conditions are met: <ol><li>Redistributions of source
code must retain the above copyright notice, this list of conditions and the following 
disclaimer.</li><li>Redistributions in binary form must reproduce the above copyright notice, this list of conditions 
and the following disclaimer in the documentation and/or other materials provided with the distribution.</li><li>Neither 
the name of the copyright holder nor the names of its contributors may be used to endorse or promote products derived 
from this software without specific prior written permission.</li></ol><codeph>THIS SOFTWARE IS PROVIDED BY THE 
COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE 
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE 
COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL 
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR 
BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT 
(INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE 
POSSIBILITY OF SUCH DAMAGE.</codeph></lq>
