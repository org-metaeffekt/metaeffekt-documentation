# Inventory Query Language (IQL)

## Introduction

The IQL (inventory query language) is a custom querying language for the {metæffekt} Inventory Index. It allows the user to simply query the database
by building custom filter expressions.

## Antlr4

To make possible creating a custom query language antlr4 is used.
Antlr4 is a parser for structured text.
To simplify: It uses a grammar file with keywords, lexers and parser rules to generate a parser that can build and walk parse trees (more details
on: https://github.com/antlr/antlr4).

## Defining a query

### Valid Expression

Valid expressions can be built by:

* Any valid `clause`
* Any expression build by a `NOT`, `AND` or `OR`
* Any expression wrapped in braces `(`, `)`

| Expression                    | 
|:------------------------------|
| **clause**                    |
| **NOT expression**            |
| **expression AND expression** |
| **expression OR expression**  |
| **'(' expression ')'**        |

### Clause

A `clause` can be build in three ways at the moment.

| Clause                                  | Explaination                                   |
|:----------------------------------------|:-----------------------------------------------|
| **field `_comparison_operator_` value** | comparing a field value to a given value       |
| **field `_set_operator_` set**          | the field value is contained in the set or not |
| **field `_emptiness_operator_`**        | the field value is empty or null               |

The `field` name is currently written in camel case.
The operators are described further in the next section.

### Valid Operators

There are three types of valid operators. Those are `comparison operators`, `set operators` and `emptiness operators`.

#### Comparison operators

As mentioned above, a comparison operator is used to compose a clause that compares a field value with another value.
Currently, these comparison operators are supported:

* `=` (equals): the field is equal to the value on the right hand side of the operator
* `!=` (not equals): the field is not equal to the value
* `<` (less than): the field is less than the value
* `<=` (less than equal): the field is less than or equal to the value
* `>` (greater than): the field is greater than the value
* `>=` (greater than equal): the field is greater than or equal to the value
* `~` (like): the field includes any substring defined by the value
* `!~` (not like): the field does not include any substring defined by the value

#### Set operators

A set operator is used to compose a clause that checks if a field value is contained in a set of values.
Currently, these set operators are supported:

* `IN` (include): a field value is contained in the set
* `NOT IN` or `! in` (not include): a field value is not contained in the right hand set

#### Emptiness operators

An emptiness operator is used to compose a clause that checks if a field value is empty or null.
Currently, these emptiness operators are supported:

* `IS EMPTY` (empty): a field is empty (empty list, empty set, ...)
* `IS NOT EMPTY` (not empty): a field is not empty
* `IS NULL_TOKEN` (null): a field is null
* `IS NOT NULL_TOKEN` (not null): a field is not null

### Value

A `value` can have by of different types. The most used ones are:

| Value          | Explaination                                                                                                                     | Example                                                     |
|:---------------|:---------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------|
| **BOOLEAN**    | true or false                                                                                                                    | true, false                                                 |
| **NUMBER**     | An integer or decimal number with an optional minus sign, but no plus sign or exponential notation.                              | 123, -123, 0, -0.5, 42.99                                   |
| **STRING**     | A string literal enclosed in either "..." or '...', with no actual line breaks, supporting escape sequences for quotation marks. | "example", "an \"escaped\" example", 'test', 'it\'s a test' |
| **IDENTIFIER** | An identifier that starts with a lowercase letter or _, followed by lowercase letters, digits, _, -, or .                        | abc_test, var1, my-variable, config.value, a_b-c.d          |
| **DURATION**   | An integer with an optional minus sign, followed by exactly one time unit (y, w, d, h, m, or s).                                 | 10d, 5h, -3w, 30s                                           |

### Examples:

Here are some examples for both view types:

#### asset-vulnerability-assessment:

* `vulnerabilityCve="CVE-2024-10039"`
* `artifactName ~ log4 and assessmentStatus = "insignificant"`
* `assessmentPriorityScore >= 5`

#### asset-vulnerable-component

* `cpe="cpe:2.3:a:redhat:keycloak:*:*:*:*:*:*:*:*"`
* `cpeVendor="keycloak" and cpeProduct="*";`
* `cpePart="*"`

