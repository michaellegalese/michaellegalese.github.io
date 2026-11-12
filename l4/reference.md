# Language Reference

The reference describes exactly what each part of L4 is and does. To learn the language step by step, take a [course](/l4/courses.md); to get a particular job done, see the [tutorials](/l4/tutorials.md).

| Go to                              | If you want to…                                                   |
| ---------------------------------- | ----------------------------------------------------------------- |
| [Glossary](/l4/reference/GLOSSARY.md)            | Look up a single keyword or symbol                                |
| [Cheat sheet](/l4/reference/cheat-sheet.md)      | Find the L4 version of code you know                              |
| [Error messages](/l4/reference/errors.md) | Understand an error or warning                                    |
| [Library](#library)                | Find a function for dates, money or lists (further down the page) |

## Language

### [Types](/l4/reference/types.md)

Describe the things your rules talk about: people, contracts, amounts, dates. The overview lists the built-in types (BOOLEAN, NUMBER, STRING, DATE) and the ready-made ones (LIST, MAYBE, EITHER).

| Page                            | What it covers                                                                                            |
| ------------------------------- | --------------------------------------------------------------------------------------------------------- |
| [DECLARE](/l4/reference/types/DECLARE.md)     | Records with HAS, enumerations with ONE OF, computed fields, type synonyms, parameterised types, A and AN |
| [ASSUME](/l4/reference/types/ASSUME.md)       | A name with a type but no value, for facts supplied from outside; how it works with `@export`             |
| [FOR ALL](/l4/reference/types/for-all.md)     | Functions that work for values of any type                                                                |
| [Coercions](/l4/reference/types/coercions.md) | Converting between NUMBER, STRING, DATE and other types                                                   |

### [Functions](/l4/reference/functions.md)

Rules in L4 are functions: they take inputs and compute a result.

| Page                                                     | What it covers                                                                   |
| -------------------------------------------------------- | -------------------------------------------------------------------------------- |
| [DECIDE](/l4/reference/functions/DECIDE.md)                            | Define a value, a function or a rule; DECIDE … IF; several clauses, one per case |
| [GIVEN and GIVETH](/l4/reference/functions/GIVEN.md)                   | Name and type the inputs; state the type of the result                           |
| [MEANS](/l4/reference/functions/MEANS.md)                              | The body of a definition, and computed fields in records                         |
| [LET and WHERE](/l4/reference/functions/LET.md)                        | Local definitions, before or after an expression                                 |
| [YIELD](/l4/reference/functions/YIELD.md)                              | Functions without a name                                                         |
| [AKA](/l4/reference/functions/AKA.md)                                  | Other names for a definition, a type or a section                                |
| [Mixfix functions](/l4/reference/functions.md#mixfix-functions) | Names with words between the inputs, so a call reads like a sentence             |

### [Control flow](/l4/reference/control-flow.md)

Choose between cases.

| Page                                 | What it covers                                                   |
| ------------------------------------ | ---------------------------------------------------------------- |
| [IF](/l4/reference/control-flow/IF.md)             | A choice between two values; several conditions                  |
| [BRANCH](/l4/reference/control-flow/BRANCH.md)     | Several conditions checked in order, with an OTHERWISE           |
| [CONSIDER](/l4/reference/control-flow/CONSIDER.md) | Choosing by the shape of a value; missing and redundant branches |
| [REFUSE](/l4/reference/control-flow/REFUSE.md)     | Declining to answer, with a reason; testing a refusal            |

### [Operators](/l4/reference/operators.md)

Combine and compare values. The overview covers word and symbol forms (`AND` or `&&`), precedence, and AND and OR across several lines (`...`, `..`).

| Page                                           | What it covers                                                   |
| ---------------------------------------------- | ---------------------------------------------------------------- |
| [AND, OR, NOT, IMPLIES](/l4/reference/operators/logical.md)  | Truth tables, conditions over several lines, order of evaluation |
| [UNLESS](/l4/reference/operators/UNLESS.md)                  | Exceptions: "A, unless B"                                        |
| [Arithmetic](/l4/reference/operators/arithmetic.md)   | PLUS, MINUS, TIMES, DIVIDED BY, MODULO; exact numbers            |
| [Comparisons](/l4/reference/operators/comparisons.md) | EQUALS, GREATER THAN, LESS THAN, AT LEAST, AT MOST               |

### [Regulative rules](/l4/reference/regulative.md)

Obligations, permissions and prohibitions: who must, may or must not do what, by when, and what follows. The overview shows the parts of a rule and how to test one with `#TRACE`.

| Page                                                     | What it covers                                                                |
| -------------------------------------------------------- | ----------------------------------------------------------------------------- |
| [PARTY, MUST, MAY, SHANT](/l4/reference/regulative/PARTY.md)           | Who acts, and whether they must, may or must not (MUST NOT); DO               |
| [Deadlines and consequences](/l4/reference/regulative/consequences.md) | WITHIN, HENCE and LEST, FULFILLED and BREACH, PROVIDED, EXACTLY, RAND and ROR |
| [BECAUSE](/l4/reference/regulative/BECAUSE.md)                         | The reason given for a breach                                                 |
| [DEONTIC](/l4/reference/regulative/DEONTIC.md)                         | The type of a regulative rule, and how a rule is evaluated                    |
| [EVENT](/l4/reference/regulative/EVENT.md)                             | What a party did and when; WAITUNTIL                                          |

### [Syntax and directives](/l4/reference/syntax.md)

How an L4 file is written, on one page:

- **Text and layout:** [layout](/l4/reference/syntax.md#layout), [comments](/l4/reference/syntax.md#comments), [identifiers](/l4/reference/syntax.md#identifiers), [literals](/l4/reference/syntax.md#literals)
- **Files:** [IMPORT](/l4/reference/syntax.md#import), [§ sections](/l4/reference/syntax.md#sections)
- **Annotations:** [`@desc`, `@ref`, `@export`](/l4/reference/syntax.md#annotations), [`@nlg`](/l4/reference/syntax/nlg.md)
- **Directives:** [`#EVAL`, `#CHECK`, `#ASSERT`, `#TRACE`](/l4/reference/syntax.md#directives)
- **Legal phrasing:** [genitive 's](/l4/reference/syntax.md#genitive), [OF](/l4/reference/syntax.md#of), [ditto ^](/l4/reference/syntax.md#ditto), [AND and OR over several lines](/l4/reference/syntax.md#asyndetic-and-and-or)

## Library

### [Built-in functions](/l4/reference/builtins.md)

Functions every L4 file can use without `IMPORT`:

- [Numbers](/l4/reference/builtins.md#numbers), [text](/l4/reference/builtins.md#text), [dates](/l4/reference/builtins.md#dates), [times of day](/l4/reference/builtins.md#times-of-day), [date and time together](/l4/reference/builtins.md#date-and-time-together)
- [Regulative traces](/l4/reference/builtins.md#regulative-traces)
- [HTTP and JSON](/l4/reference/builtins/http-json.md): FETCH, POST, ENV, JSONENCODE, JSONDECODE

### [Standard libraries](/l4/reference/libraries.md)

Libraries you load with [`IMPORT`](/l4/reference/libraries.md#import):

| Group                     | Libraries                                                                                                                                                               |
| ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Core                      | [prelude](/l4/reference/libraries/prelude.md) (lists, MAYBE, EITHER, pairs), [math](/l4/reference/libraries/math.md)                                                                                |
| Dates and time            | [daydate](/l4/reference/libraries/daydate.md), [time](/l4/reference/libraries/time.md), [datetime](/l4/reference/libraries/datetime.md), [timezone](/l4/reference/libraries/timezone.md), [excel-date](/l4/reference/libraries/excel-date.md) |
| Money and finance         | [currency](/l4/reference/libraries/currency.md), [holdings](/l4/reference/libraries/holdings.md), [actus](/l4/reference/libraries/actus.md)                                                                       |
| Legal entities and places | [legal-persons](/l4/reference/libraries/legal-persons.md), [jurisdiction](/l4/reference/libraries/jurisdiction.md)                                                                                  |
| AI                        | [llm](/l4/reference/libraries/llm.md)                                                                                                                                                 |

### [Error messages](/l4/reference/errors.md)

What the compiler's errors and warnings mean and how to fix them, grouped by kind: [layout and syntax](/l4/reference/errors.md#layout-and-syntax), [types](/l4/reference/errors.md#types), [names and imports](/l4/reference/errors.md#names-and-imports), [definitions and clauses](/l4/reference/errors.md#definitions-and-clauses), [pattern matching](/l4/reference/errors.md#pattern-matching), [regulative rules](/l4/reference/errors.md#regulative-rules) and [export](/l4/reference/errors.md#export).
