# Universal Assessment Vector (UAV)

This document describes the Universal Assessment Vector (UAV), which mediates between seven
sources in two families: four attribute frameworks and three operational vocabularies. It
bridges the gap between theoretical data attributes (Parker/CIA) and operational control
failures (MITRE/BSI).

**Attribute frameworks** define security properties directly:

* The CIA Triad: Confidentiality, Integrity and Availability.
* The Parkerian Hexad: An atomic, non-overlapping extension of CIA adding Possession, Authenticity, and Utility.
* BSI IT-Grundschutz: A management framework focusing on three core values and extended glossary terms like Binding Character and Reliability.
* ISO/IEC 27000: Defines information security as preservation of Confidentiality, Integrity and Availability, naming Authenticity, Accountability, Non-Repudiation and Reliability as properties that may also be involved.

**Operational vocabularies** enumerate consequences rather than properties, but the
consequences they name are attribute-shaped, and two metrics exist only because of them:

* MITRE CWE/CAPEC: Weakness and attack-pattern enumeration, with a nine-value consequence Scope. https://threat-modeling.com/capec-threat-modeling/
* MITRE ATT&CK: Adversary technique enumeration; its Impact tactic names what an adversary achieves, including Loss of Safety and Damage to Property.
* CVSS: Vulnerability scoring, whose impact metrics are Confidentiality, Integrity and Availability, joined in v4.0 by Safety.

STRIDE appears throughout this document as a cross-reference, not as a fifth source
framework. It classifies threats rather than defining security attributes, so it
contributes no metric of its own; it has no counterpart to Possession or Utility.
CAPEC attack patterns are commonly mapped onto STRIDE categories, which is why STRIDE
terms are cited alongside CWE and CAPEC below.

> **Reading order.** Part I states what the model *is*: the aspects, the twelve metrics,
> how they map onto the sources, and the conflicts they resolve. Part II states how it is
> *written down and computed*: the vector string, the value scale, how an aspect is
> established, and the operators. A reader who only needs to interpret an assessment can
> stop at the end of Part I.

---

# Part I: Concepts

## The Three Aspects

The Universal Assessment Vector is not bound to a single kind of subject. The same twelve
metrics express three distinct assessment aspects:

| Aspect       | Subject                        | Question answered                          |
|--------------|--------------------------------|--------------------------------------------|
| **impacts**  | A threat or attack pattern     | What does this threat damage?              |
| **protects** | A control or safeguard         | What does this control protect?            |
| **demands**  | An asset, system or capability | What protection does this subject require? |

Sharing one metric set across all three aspects is the purpose of the model. Because
what a threat impacts, what a control protects and what an asset demands are all
expressed in the same twelve metrics, they can be related to each other arithmetically
rather than merely filed side by side: a control can be shown to protect what an asset
demands, or a threat to strike where nothing protects. A framework that assesses
threats in one vocabulary and assets in another cannot make that statement.

### Polarity

The aspects do not share a direction of "good":

* On **impacts**, a **higher** value is **worse**, the threat damages more.
* On **protects**, a **higher** value is **better**, the control protects more.
* On **demands**, a value is neither good nor bad. It states a requirement.

Consequently values from different aspects must never be averaged or summed into a
single figure. They are combined only through the [Operators](#operators) in Part II,
which are defined with the polarity built in.

## The Twelve Metrics

The following 12 metrics represent a universal set required to map all four attribute
frameworks. Note that further metrics may be added, or metrics deprecated, over time.

The last two, Assurance and Safety, are not attributes of any of the four *attribute*
frameworks. They enter from the operational vocabularies, which name consequences those
frameworks cannot express: Assurance from the CWE Impact enumeration, Safety from the
ATT&CK Impact tactic and from CVSS. The matrix below marks this directly.

The twelve are not all of one kind, and the distinction decides which metric a finding
belongs to:

* Properties of the information, namely Confidentiality, Integrity, Availability, Possession,
  Utility and Authenticity, state what is true of the data itself, or of the functions serving it.
* Properties of the governing controls, namely Authentication, Authorization, Non-Repudiation
  and Accountability, state what is true of the mechanisms deciding who may act, and of what
  can later be proven about what they did.
* Properties beyond the information are Assurance, the degree to which the artefact can be
  reviewed at all, and Safety, the consequences in the physical world.

A finding that names a defeated mechanism belongs in the second group even when the damage
lands in the first: a bypassed permission check is `Az`, and whatever was then read is `C`.
Both are asserted, and they are not substitutes.

### What Each Metric Asserts

Each key names one concern. What a value *asserts* about that concern depends on the
aspect, so each metric is stated three times, once per perspective. A definition given
only from the impacts side ("Integrity is impacted by tampering") does not tell an
assessor what to write on a control or on an asset.

| Key                      | A threat **impacts**                                                                                                             | A control **protects**                                                                           | An asset **demands**                                                                 |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------|
| **C** (Confidentiality)  | Discloses information to unauthorized parties.                                                                                   | Prevents unauthorized disclosure.                                                                | Requires information to stay undisclosed.                                            |
| **I** (Integrity)        | Changes the stored state without authorization.                                                                                  | Prevents or detects unauthorized change of the stored state.                                     | Requires the stored state to change only through authorized action.                  |
| **A** (Availability)     | Disrupts timely access to information or systems.                                                                                | Keeps information and systems reachable when needed.                                             | Requires timely and reliable access.                                                 |
| **P** (Possession)       | Removes exclusive custody, a copy leaves the owner's control, even if unreadable.                                                | Keeps custody exclusive.                                                                         | Requires custody to remain exclusive.                                                |
| **U** (Utility)          | Renders data or a function unusable although present: lost keys, obsolete format, behaviour that runs but cannot be relied upon. | Keeps data and functions in a usable form.                                                       | Requires data and functions to remain usable, not merely present.                    |
| **Au** (Authenticity)    | Undermines trust in the data's veracity or in the genuineness of a claimed source (e.g. STRIDE's Spoofing).                      | Establishes that data and claimed sources are genuine.                                           | Requires data and source identity to be verifiable as genuine.                       |
| **An** (Authentication)  | Defeats or bypasses the ability to authenticate actors.                                                                          | Provides the capability to authenticate actors.                                                  | Requires actors to be authenticated.                                                 |
| **Az** (Authorization)   | Escalates privilege or bypasses permission checks.                                                                               | Enforces permission checks before an action is performed.                                        | Requires actions to be permitted before they are performed.                          |
| **Nr** (Non-Repudiation) | Destroys the proof that a transaction occurred.                                                                                  | Produces proof that a transaction occurred (Binding Character).                                  | Requires transactions to be provable after the fact.                                 |
| **Ac** (Accountability)  | Removes the trail needed to attribute actions to entities.                                                                       | Records actions so they can be attributed to entities.                                           | Requires actions to be traceable to specific entities.                               |
| **As** (Assurance)       | Degrades the system's reviewability or maintainability, so that its security can no longer be established or kept.               | Makes the system reviewable and maintainable: review, analysis, documentation, coding standards. | Requires the system to remain analysable enough for its security to be demonstrated. |
| **Sf** (Safety)          | Causes harm to people, property or the environment.                                                                              | Prevents such harm: interlocks, protective functions, emergency shutdown.                        | Requires freedom from harm to people, property or the environment.                   |

**On Integrity.** The impacts column says *without authorization* deliberately. Integrity
turns on whether the stored state was changed by an authorized party, not on whether the
original could in principle be recovered. Data encrypted in place by a hostile party is an
Integrity breach even though the plaintext is recoverable with the key; the same data
encrypted by its owner, who then loses the key, is not. Utility is lost in both cases.
See [The "Availability vs. Utility" Difference](#the-availability-vs-utility-difference).

**On Authenticity versus Authentication.** `Au` is a property of the data or of a claimed
identity; `An` is the capability that verifies such a claim. A forged document with no
authentication involved is `Au` alone; a defeated login mechanism is `An`.

### Where Each Metric Comes From

The entries below record each metric's provenance and boundary: which source supplies it,
and where it stops. They justify the table above rather than restating it.

#### Confidentiality
* The property that information is not made available or disclosed to unauthorized individuals, entities, or processes.
* Universally accepted by CIA, Parker, BSI, and MITRE.

#### Integrity
* The state of being whole, sound, and unimpaired.
* The BSI defines this strictly as "correctness". However, the Parkerian Hexad distinguishes integrity from correctness, noting that data can be "whole" (integral) but factually wrong. This metric accepts Parker’s granular definition to separate technical state from factual truth.
* https://en.wikipedia.org/wiki/Parkerian_Hexad#Integrity

#### Availability
* Timely and reliable access to information or systems.

#### Possession (or Control)
* The physical or logical custody of information, distinct from the ability to read or modify it.
* Unique to Parker. It covers scenarios like the theft of encrypted media (Loss of Possession without Loss of Confidentiality) which other frameworks struggle to classify accurately.

#### Utility
* The usefulness of data **or of a function**, distinct from binary availability. For data this covers correct format and accessible encryption keys; for a function it covers behaviour that executes but yields results that cannot be relied upon or used.
* Unique to Parker, whose formulation is data-centric. This metric widens it to functions, because a service that runs and responds but violates its contract is neither unavailable (Availability) nor incorrectly stored (Integrity); it is present but unusable, which is precisely the Utility case.
* It distinguishes between "data is missing" (Availability) and "data is present but unusable" (Utility), such as in cases of lost decryption keys or obsolete file formats.

#### Authenticity
* The validity, genuineness, and conformity to reality of information or identity.
* Maps to data truthfulness, Parker’s "conformity to fact". https://en.wikipedia.org/wiki/Parkerian_Hexad#Authenticity
* Boundary: Authenticity is a property of the data or of a claimed identity. The mechanism that verifies a claimed identity is covered by Authentication.

#### Authentication
* The capability to authenticate individual person, systems and subsystems.
* Included in MITRE Scope Enumeration.
* MITRE also defines Access Control. In the Universal Vector Access Control is the combination of Authentication and Authorization. We currently decided to go for atomic metrics instead of combining these.
* Identity Verification: MITRE’s "Authentication" scope and CAPEC’s "Identity Spoofing" attack pattern (CAPEC-151) (Is the user who they say they are?).

#### Authorization
* The state of having the permission to perform a specific action.
* Source: Explicitly defined in the MITRE Scope Enumeration and in STRIDE’s "Elevation of Privilege."
* Boundary: A property of the access-control mechanism rather than of the information it governs; see the metric kinds above. Authentication, Non-Repudiation and Accountability share that character; Authorization is not unique in it.

#### Non-Repudiation (and Binding Character)
* The inability to deny the validity of a signature or the creation of a transaction, resulting in a legally or organizationally binding relationship.
* The BSI IT-Grundschutz explicitly defines Binding Character as the combined security objective of Authenticity and Non-Repudiation.
* While Non-Repudiation provides the technical proof that an event occurred (e.g., a digital receipt), Binding Character is the resulting state where the identity of the source is proven (Authenticity) and the action cannot be denied (Non-Repudiation).
* Source Mapping:
  * MITRE: Maps directly to the "Non-Repudiation" scope.
  * BSI IT-Grundschutz: Maps to "Binding Character" as a high-level goal reliant on cryptographic proof and identity verification.

#### Accountability
* The ability to trace actions uniquely to a specific entity for audit purposes.
* Unique to MITRE: Defined in the MITRE Scope Enumeration. It addresses the requirement for logging and attribution, which pure data-attribute models often overlook.

#### Assurance
* The degree to which a system can be understood, reviewed and maintained well enough for its security properties to be established and kept.
* The other metrics describe *functional* security properties: what holds, or fails to hold, right now. Assurance is the orthogonal axis: the grounds for confidence that those properties hold at all. Common Criteria (ISO/IEC 15408) draws the same line between Security Functional Requirements and Security Assurance Requirements.
* Source: MITRE's CWE Impact enumeration carries `Reduce Maintainability`, `Quality Degradation` and `Increase Analytical Complexity`, 145 consequences across 110 of the 944 active CWEs. The CWE *Scope* enumeration has no matching value, so these are filed under `Other`; Assurance gives them a home.
* Boundary: Assurance covers degradation that impairs establishing or maintaining security: excessive complexity, insufficient encapsulation, missing documentation, unsafe constructs, specification violations. It is not a general software-quality score. `Reduce Performance` and `Reduce Reliability` describe runtime behaviour and belong to Availability.
* Narrow reading: the metric records threats whose **effect** is degraded reviewability, not defect classes that merely happen to be difficult to analyse. A race condition is hard to review, but what it damages is timing-dependent correctness, so it carries Integrity and Availability rather than Assurance. A coding-standards violation damages reviewability itself, so it carries Assurance. The test is what the threat degrades, not how hard the threat is to find.

#### Safety
* Freedom from harm to people, property or the environment arising from the behaviour of the assessed system.
* Not an information property, and deliberately so. IEC 62443 orders the objectives of an industrial system **Safety, Integrity, Availability, Confidentiality**, inverting the IT convention and treating safety as a peer objective rather than a downstream consequence of a security failure.
* Source: MITRE ATT&CK for ICS defines `Loss of Safety`, `Damage to Property` and `Loss of Protection` as techniques of its Impact tactic. None of the other eleven metrics can express them: a threat that injures an operator would otherwise be recorded as `A:H`, indistinguishable from a service outage.
* Corroboration: CVSS v4.0 carries Safety as a supplemental metric and as a value of `MSI` / `MSA` ranked above High, defining it against IEC 61508 consequence categories. Two independent scoring systems reaching for the same concept, from the same standards family, is the argument for treating it as a metric rather than as a consequence of Availability.
* Boundary: harm to people, property and environment. Business consequences (lost production, revenue, reputation) stay out, on the same grounds that keep them out of every other metric. ATT&CK's `Loss of Productivity and Revenue` is therefore not mapped.

## Mapping onto the Source Frameworks

The first four columns are **attribute frameworks**; the last three are **operational
vocabularies**. Reading a row across shows which sources name the metric and which merely
imply or omit it.

| Concluded Metric | CIA | Parkerian | BSI | ISO 27000 | CWE/CAPEC | ATT&CK | CVSS |
|------------------|-----|-----------|-----|-----------|-----------|--------|------|
| Confidentiality  | X   | X         | X   | X         | X         | X      | X    |
| Integrity        | X   | X         | X   | X         | X         | X      | X    |
| Availability     | X   | X         | X   | X         | X         | X      | X    |
| Possession       | -   | X         | -   | -         | -         | -      | -    |
| Utility          | (X) | X         | -   | -         | -         | +      | -    |
| Authenticity     | (X) | X         | +   | +         | -         | +      | -    |
| Authentication   | -   | -         | -   | -         | X         | +      | -    |
| Authorization    | -   | -         | -   | -         | X         | +      | -    |
| Non-Repudiation  | (X) | -         | +   | +         | X         | -      | -    |
| Accountability   | -   | -         | (X) | +         | X         | +      | -    |
| Assurance        | -   | -         | -   | -         | +         | -      | -    |
| Safety           | -   | -         | -   | -         | -         | X      | X    |

Legend:

* `X`: Defined as a core attribute, or named directly in the vocabulary.
* `-`: Not expressible in this source.
* `(X)`: Implied by another attribute rather than named.
* `+`: Named in the source's secondary or operational vocabulary (an extended glossary,
  a note to entry, or a technique/impact enumeration) rather than as a primary attribute.

**On the ISO/IEC 27000 column.** ISO defines information security as preservation of
Confidentiality, Integrity and Availability; those three are `X`. Authenticity,
Accountability and Non-Repudiation are named in a note to that definition as properties
that *can also be involved*, which is `+` by the legend above, real standards-body
support, but secondary to the core three. Reliability is named alongside them and is
deliberately absent as a row: this framework treats it as the composite outcome of
Confidentiality, Integrity and Availability, as recorded under Resolved Conflicts. The
standard itself is paywalled and could not be fetched; this row reflects the widely
quoted text of clause 3.28 and should be checked against the published standard before
the document is released.

**On the two metrics no attribute framework supplies.** Assurance and Safety score `-`
across all four attribute frameworks, and that is the finding rather than an oversight.
They are included because assessments demonstrably need them, and each is carried by an
operational vocabulary instead: Assurance by the CWE Impact enumeration (`Reduce
Maintainability`, `Quality Degradation`, `Increase Analytical Complexity`), Safety by the
ATT&CK Impact tactic (`Loss of Safety`, `Damage to Property`, `Loss of Protection`) and by
CVSS v4.0, which carries it as a supplemental metric and as a value of `MSI`/`MSA` ranked
above High. Safety is the stronger case of the two: two independent scoring systems name
it outright, both against IEC 61508 consequence categories. Assurance has one source plus
a standards precedent in Common Criteria (ISO/IEC 15408), which separates Security
Functional Requirements from Security Assurance Requirements on the same line this metric
draws.

## Resolved Conflicts

### The "Integrity" Differences
* BSI equates integrity with "correctness/intactness."
* Parker defines integrity as "wholeness" but notes it is "not necessarily correct."
* Resolution: The Concluded Metric separates Integrity (technical state) from Authenticity (truthfulness). This allows for a more precise analysis where data can be technically uncorrupted yet factually false.

### The "Reliability" Composition (BSI)
* Observation: The BSI IT-Grundschutz lists "Reliability" in its glossary.
* Resolution: The BSI documentation defines reliability as the successful combination of ensuring "availability, integrity, and confidentiality." Therefore, Reliability is not treated as an additional atomic metric in this framework, but as the successful composite outcome of Confidentiality, Integrity and Availability working together.

### The "Availability vs. Utility" Difference
* Scenario: Data is encrypted, but the key is lost.
* Standard View (CIA/BSI): Classifies this as an Availability breach (information cannot be accessed).
* Parker's View: Classifies this as a Utility breach. The bits are available and uncorrupted, but not useful as the representation changed.
* Resolution: We include Utility to allow for precise classification of crypto-shredding or format obsolescence, distinct from system crashes.
* Note on who encrypted: the bits are uncorrupted here because the owner encrypted them and then lost the key; no unauthorized change occurred. Where a hostile party encrypts data in place, Integrity *is* breached, because Integrity turns on whether the stored state was changed by an authorized party, not on whether the plaintext could in principle be recovered. Utility is lost in both cases; Integrity separates them. See the [ransomware example](#ransomware-crypto-locker).

### The "Confidentiality vs. Possession" Difference
* Scenario: Theft of a sealed, encrypted hard drive.
* Standard View: Often flagged as a Confidentiality risk (potential exposure) or Availability risk (loss of data).
* Parker's View: Confidentiality is not breached (thief cannot read it), but Possession is lost.
* Resolution: We include Possession to accurately categorize physical security incidents where data remains secret but is no longer under the owner's control.

---

# Part II: Specification

## The Vector String

To use the Universal Assessment Vector for assessments, we propose the UAV string. Similar to the CVSS vector string, this provides a compact, machine-readable representation of an assessment across all 12 metrics.

### Structure

```
UAV:1.0/C:[x]/I:[x]/A:[x]/P:[x]/U:[x]/Au:[x]/An:[x]/Az:[x]/Nr:[x]/Ac:[x]/As:[x]/Sf:[x]
```

The `1.0` element carries the version of the metric set. Since metrics may be
added or deprecated over time, a consumer must be able to tell which generation
of the vector it is parsing.

The same string shape serves all three aspects; which one applies follows from the
carrier, as described under [Establishing the Aspect](#establishing-the-aspect).

**Metrics may be omitted.** An omitted metric is `U` (Undefined). A vector states what has
been determined and stays silent about the rest, so the full and the abbreviated form are
equivalent:

```
UAV:1.0/C:U/I:U/A:H/P:U/U:U/Au:U/An:U/Az:U/Nr:U/Ac:L
UAV:1.0/A:H/Ac:L
```

This does not extend to `N`. Silence is `U`, never `N`: a metric is `N` only where an
assessor positively determined that it is not affected. The distinction matters because
`N` closes a metric for every path through the subject, whereas `U` leaves it open.

**Metric order is free; the order above is recommended.** A parser must accept the metrics
in any order, because the string is a set of key/value pairs rather than a sequence. Writers
should nonetheless emit the documented order: it makes vectors comparable by eye and diffable
line by line, which matters when a catalog holds hundreds of them. Every vector this
repository emits uses it.

**An unrecognised key is an error.** A consumer that meets a key it does not know must reject
the vector rather than ignore the pair; silently dropping an unknown metric would understate
the assessment, and understating is the one failure mode this vector exists to prevent. A key
appearing more than once is malformed on the same grounds.

That rule is what makes the version element load-bearing. A `1.0` consumer meeting a `1.1`
string that carries a metric added after `1.0` must reject it, not parse the part it
recognises. Forward compatibility is therefore by **explicit version check**, never by lenient
parsing: a consumer reads the version first, and declines what it cannot fully represent.

### Metric Values ([x])

Every value carries an aspect-specific reading. The first four assign a degree; the last
two record that no degree was assigned:

| Value             | A threat **impacts**                                                                                    | A control **protects**                                                             | An asset **demands**                                                                                                   |
|-------------------|---------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| **N** (None)      | Does not affect this metric.                                                                            | Contributes nothing to this metric.                                                | Requires no protection.                                                                                                |
| **L** (Low)       | Damages it to a limited extent; absorbed by standard processes.                                         | Protects it basically; raises the bar but is not relied upon.                      | Requires normal protection (BSI: *normal*).                                                                            |
| **M** (Medium)    | Damages it significantly; noticeable loss.                                                              | Protects it substantially; effective against ordinary attackers.                   | Requires high protection (BSI: *hoch*).                                                                                |
| **H** (High)      | Damages it catastrophically; threatens "Crown Jewels" or organizational existence.                      | Protects it strongly and assuredly; relied upon as a primary safeguard.            | Requires very high protection (BSI: *sehr hoch*).                                                                      |
| **U** (Undefined) | Not assessed: the damage is unknown, or the assessor did not want to decide. The metric stays in scope. | Not assessed: the contribution is unknown or undecided. The metric stays in scope. | Not assessed: the requirement is unknown or undecided. The metric stays in scope.                                      |
| **I** (Ignore)    | Deliberately out of scope for this threat; the outcome is not of interest.                              | Deliberately out of scope for this control; the outcome is not of interest.        | The subject owner declares the metric out of scope; the decision propagates to every comparison involving the subject. |

`U` and `I` both record the absence of a decision, but they are not interchangeable. `U`
leaves the metric in scope and its outcome relevant; it may still be used to further
judge the metric. `I` removes the metric from consideration entirely.
[Comparison Operators](#comparison-operators) defines how each propagates through the
operators.

`U` was chosen in deliberate preference to `X`, which CVSS uses for "Not Defined". The
two are not equivalent: in CVSS, `X` directs a consumer to fall back to a default or
base value, whereas `U` here records that the outcome is genuinely unknown and remains
relevant to the assessment. Adopting `X` would import the defaulting semantic from CVSS
and misstate the intent.

## Establishing the Aspect

The aspect is **not encoded in the vector**. It follows from the context in which the
vector is used, the field, document or assessment that carries it. A vector recorded
against a threat states what that threat *impacts*; one recorded against a control
states what it *protects*; one recorded against an asset or capability states what it
*demands*.

This keeps the string short at the cost of making it context-dependent: the same
characters `C:H` read as "disclosure would be catastrophic" on a threat and as "this
subject requires the strongest available confidentiality protection" on an asset,
statements that call for opposite responses. Two obligations follow, and neither is
optional:

* A carrier must fix the aspect unambiguously for every vector it holds. A field that
  could plausibly hold two aspects is a defect in the carrier.
* A vector reproduced outside its carrier (in a report, an export, a log entry) must
  be accompanied by its aspect. Quoted bare, it does not carry its own meaning.

### Where Each Aspect Is Recorded

Since the aspect is established by the carrier rather than by the vector, the following
assignment is normative; it is the only thing that determines how a stored vector is
to be read:

| Aspect       | Carrier                                                                                                                                                   |
|--------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| **impacts**  | Threat catalog entries, see the `impactAssessments` field. <!-- TODO: link the threat catalog format document once it is available in this repository --> |
| **protects** | Control catalog entries, e.g. the BSI "Stand der Technik" Kernel controls.                                                                                |
| **demands**  | Asset and capability inventories. Not yet represented in this repository.                                                                                 |

Note that the `impactAssessments` field name predates this model and is narrower than
the model requires; carriers for protects and demands vectors need a correspondingly
aspect-neutral field.

## Operators

### Comparison Operators

Comparison operators relate two vectors of **different** aspects to each other. They are
the reason the three aspects share one metric set.

Let `rank(N)=0`, `rank(L)=1`, `rank(M)=2`, `rank(H)=3`, and let `m` range over the twelve
metrics. Three comparison operators produce a new vector from two input vectors:

| Operator              | Definition                                  | Reading                                                                                    |
|-----------------------|---------------------------------------------|--------------------------------------------------------------------------------------------|
| **Coverage gap**      | `gap(m) = max(0, demands(m) − protects(m))` | Protection required but not delivered.                                                     |
| **Residual exposure** | `res(m) = max(0, impacts(m) − protects(m))` | Damage the controls in place do not prevent.                                               |
| **Risk relevance**    | `rel(m) = min(impacts(m), demands(m))`      | A threat striking a metric the subject does not require protects itself out of the result. |

Handling of the two non-scoring values is deliberate and must not be shortcut:

* If either operand is **Undefined**, the result for that metric is **Undefined**, never
  zero. An unassessed metric is not the same as an assessed absence of gap, and
  collapsing the two silently converts ignorance into assurance.
* If the **demands** operand is **Ignore**, the result for that metric is **Ignore**. A
  subject owner declaring a metric out of scope propagates that decision through every
  comparison involving that subject.

**The result is a UAV.** Each operator is defined per metric and returns a full vector, not
a scalar: `gap(demands, protects)` yields a UAV whose `C` is the confidentiality gap, whose
`I` is the integrity gap, and so on. Integer results map back through `rank⁻¹` (`0` → `N`,
`1` → `L`, `2` → `M`, `3` → `H`), so a derived vector carries the same twelve keys and the
same value scale as its inputs, and every rule in this document applies to it unchanged.

**A derived vector's aspect is the operator.** It is not impacts, protects or demands; it
states a deficit or an overlap between two of them. Where an assessed vector takes its aspect
from its carrier, a derived vector takes it from the operator that produced it: *coverage
gap*, *residual exposure*, *risk relevance*. It must be labelled accordingly wherever it is
stored or quoted, on the same grounds given under
[Establishing the Aspect](#establishing-the-aspect): the twelve keys and six values are shared,
so nothing but the label says what the numbers mean.

**No scalar is implied, and none is needed here.** Reducing twelve dimensions to one number
requires weights this document deliberately does not fix, and the comparison operators do not
attempt it. Where a scalar is genuinely wanted it belongs in a separate function over a single
UAV, a `risk(UAV)`. That shape already exists: `s(x)` in
[`threat-prioritization.md`](threat-prioritization.md) maps a UAV to a score in `[-2, +2]`,
and applies to a gap or residual-exposure vector exactly as it does to an impacts vector.

### Comparison versus Propagation

A second, distinct kind of operator exists. Where a **comparison** operator relates two
vectors of *different* aspects, a **propagation** operator carries *one* aspect along a
chain of related entities, for example a threat's impacts propagated through the CAPEC,
CWE and CVE entries that realise it. Propagation is defined in
[`threat-prioritization.md`](threat-prioritization.md).

The two must not be conflated, because they treat **Undefined** in opposite ways and both
treatments are deliberate:

|                 | Operands                 | Undefined                                                                                                                         |
|-----------------|--------------------------|-----------------------------------------------------------------------------------------------------------------------------------|
| **Comparison**  | two different aspects    | propagates as Undefined, an unassessed metric must not read as an assessed absence                                                |
| **Propagation** | one aspect, along a path | yields to the other operand, an intermediate entity that states nothing about a metric must not erase what is already established |

## Worked Example

### Ransomware (Crypto-Locker)

This example assesses the **impacts** aspect. The vector is carried by a threat, which is
what establishes the aspect.

Scenario: A server holds semi-open data, some already published, some internal only. The
attacker escalates to system privileges, copies the data, encrypts it in place, and
clears the host's event logs. The server keeps running, so the bits remain reachable, but
nothing can read them.

A CIA-only assessment has no vocabulary for "present but unusable", so it is forced to
record the loss as `A:H`, filing as an outage what is in fact a loss of utility.

The UAV string reads:

```
UAV:1.0/C:M/I:H/A:N/P:H/U:H/Au:N/An:N/Az:H/Nr:N/Ac:M/As:N/Sf:N
```

* C:M (Confidentiality): A copy was taken, but the data is semi-open; disclosure is damaging rather than catastrophic.
* I:H (Integrity): The attacker rewrote the stored bytes without authorization. Integrity here tracks whether the state was changed by an authorized party, not merely whether the plaintext could in principle be recovered.
* A:N (Availability): The server is up and the data is reachable. Access was not disrupted.
* P:H (Possession): Exclusive custody is lost, the attacker holds a copy, even though the organization still holds its own drives.
* U:H (Utility): The data is present and reachable, yet useless without the key.
* Au:N (Authenticity): Nothing was forged; no source identity was falsified.
* An:N (Authentication): The capability to authenticate actors is unaffected.
* Az:H (Authorization): Escalation to system privileges defeated the authorization controls outright.
* Nr:N (Non-Repudiation): No transaction proof was weakened.
* Ac:M (Accountability): The cleared event logs remove the trail needed to attribute actions on the host.
* As:N (Assurance): The attack encrypts and exfiltrates data; it does not degrade the system's own reviewability or maintainability.
* Sf:N (Safety): The scenario is confined to data confidentiality, integrity and availability; no harm to people, property or the environment results.

## References

Attribute frameworks:

* ISO/IEC 27000, Information security management systems, overview and vocabulary (clause 3.28 defines information security). https://www.iso.org/standard/73906.html
* https://en.wikipedia.org/wiki/Parkerian_Hexad
* https://www.bsi.bund.de/SharedDocs/Downloads/EN/BSI/Grundschutz/International/bsi_it_gs_comp_2022.pdf?__blob=publicationFile&v=2
* https://www.bsi.bund.de/SharedDocs/Downloads/EN/BSI/Grundschutz/International/Basic_Security.pdf?__blob=publicationFile&v=2

Operational vocabularies:

* https://cwe.mitre.org/
* https://capec.mitre.org/
* https://attack.mitre.org/, Impact tactic; ICS matrix for Safety-related techniques
* https://www.first.org/cvss/v4.0/specification-document
* https://www.first.org/cvss/v3.1/specification-document
* https://threat-modeling.com/capec-threat-modeling/

Standards behind the two added metrics:

* ISO/IEC 15408 (Common Criteria), the Functional / Assurance Requirements split behind Assurance.
* IEC 62443, orders industrial security objectives Safety, Integrity, Availability, Confidentiality.
* IEC 61508, the consequence categories CVSS and this framework both reference for Safety.

Cross-reference:

* https://en.wikipedia.org/wiki/STRIDE_model
