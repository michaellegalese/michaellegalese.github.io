# Glossary

Every keyword, operator, type, directive and annotation in L4, with a one-line meaning and a link to the page that describes it. Functions are listed under [Built-in functions](/l4/reference/builtins.md) and [Standard libraries](/l4/reference/libraries.md).

## Functions

| Keyword                | Meaning                                                                  | Reference                                     |
| ---------------------- | ------------------------------------------------------------------------ | --------------------------------------------- |
| **DECIDE**             | Defines a value, a function or a rule; one definition or several clauses | [DECIDE](/l4/reference/functions/DECIDE.md)                 |
| **MEANS**              | Gives the body of a function, a value or a computed field                | [MEANS](/l4/reference/functions/MEANS.md)                   |
| **IS**                 | Gives the body after `DECIDE name`; also states a type (`IS A NUMBER`)   | [DECIDE](/l4/reference/functions/DECIDE.md)                 |
| **IF** (after DECIDE)  | `DECIDE x IF condition` defines a BOOLEAN                                | [DECIDE](/l4/reference/functions/DECIDE.md)                 |
| **GIVEN**              | Names and types the inputs of a function                                 | [GIVEN and GIVETH](/l4/reference/functions/GIVEN.md)        |
| **GIVETH** / **GIVES** | States the type of the result                                            | [GIVEN and GIVETH](/l4/reference/functions/GIVEN.md#giveth) |
| **LET** ... **IN**     | Local definitions before an expression                                   | [LET and WHERE](/l4/reference/functions/LET.md)             |
| **BE** / **MEAN**      | Alternatives to IS and MEANS in a LET binding                            | [LET and WHERE](/l4/reference/functions/LET.md)             |
| **WHERE**              | Local definitions after an expression                                    | [LET and WHERE](/l4/reference/functions/LET.md#where)       |
| **YIELD**              | A function without a name: `GIVEN n YIELD n + 1`                         | [YIELD](/l4/reference/functions/YIELD.md)                   |
| **AKA**                | Another name for the same function, value, type or section               | [AKA](/l4/reference/functions/AKA.md)                       |
| Several clauses        | A rule written as one line per case: `DECIDE price Red IS 1`             | [DECIDE](/l4/reference/functions/DECIDE.md#several-clauses) |

## Types

| Keyword                      | Meaning                                                                            | Reference                       |
| ---------------------------- | ---------------------------------------------------------------------------------- | ------------------------------- |
| **DECLARE**                  | Defines a new type                                                                 | [DECLARE](/l4/reference/types/DECLARE.md)     |
| **HAS**                      | Lists the fields of a record, including computed fields with MEANS                 | [DECLARE](/l4/reference/types/DECLARE.md)     |
| **ONE OF**                   | Lists the alternatives of an enumeration                                           | [DECLARE](/l4/reference/types/DECLARE.md)     |
| **IS A** / **IS AN**         | States a type: `age IS A NUMBER`                                                   | [DECLARE](/l4/reference/types/DECLARE.md)     |
| **A** / **AN** / **THE**     | Articles before a type name; they do not change the meaning                        | [DECLARE](/l4/reference/types/DECLARE.md)     |
| **OF**                       | Applies a type or a constructor to its arguments: `LIST OF NUMBER`, `PAIR OF x, y` | [DECLARE](/l4/reference/types/DECLARE.md)     |
| **WITH**                     | Builds a record by field name: `Person WITH name IS "Ann"`                         | [DECLARE](/l4/reference/types/DECLARE.md)     |
| **ASSUME**                   | Declares a name with a type but no definition                                      | [ASSUME](/l4/reference/types/ASSUME.md)       |
| **TYPE**                     | The type of types, for type inputs: `GIVEN a IS A TYPE`                            | [FOR ALL](/l4/reference/types/for-all.md)     |
| **FOR ALL**                  | Introduces type variables in a type                                                | [FOR ALL](/l4/reference/types/for-all.md)     |
| **FUNCTION FROM** ... **TO** | A function type: `FUNCTION FROM NUMBER TO BOOLEAN`                                 | [Types](/l4/reference/types.md)        |
| **AS**                       | `x AS STRING` turns a value into text                                              | [Coercions](/l4/reference/types/coercions.md) |

## Built-in types

| Type           | Meaning                                                           | Reference                                    |
| -------------- | ----------------------------------------------------------------- | -------------------------------------------- |
| **NUMBER**     | Whole numbers and exact fractions                                 | [Types](/l4/reference/types.md)                     |
| **STRING**     | Text                                                              | [Types](/l4/reference/types.md)                     |
| **BOOLEAN**    | `TRUE` or `FALSE`                                                 | [Types](/l4/reference/types.md)                     |
| **DATE**       | A calendar date                                                   | [Types](/l4/reference/types.md)                     |
| **TIME**       | A time of day, with no date or time zone                          | [Types](/l4/reference/types.md)                     |
| **DATETIME**   | A moment in time, with a time zone                                | [Types](/l4/reference/types.md)                     |
| **LIST**       | A list of values of one type; also writes a list: `LIST 1, 2, 3`  | [Types](/l4/reference/types.md)                     |
| **MAYBE**      | A value that may be missing: `JUST x` or `NOTHING`                | [Types](/l4/reference/types.md)                     |
| **EITHER**     | One of two values: `LEFT x` or `RIGHT y`                          | [Types](/l4/reference/types.md)                     |
| **PAIR**       | Two values together, from the prelude                             | [prelude](/l4/reference/libraries/prelude.md#pairs)        |
| **Dictionary** | Keys mapped to values, from the prelude                           | [prelude](/l4/reference/libraries/prelude.md#dictionaries) |
| **DEONTIC**    | The type of a regulative rule: `DEONTIC party action`             | [DEONTIC](/l4/reference/regulative/DEONTIC.md)             |
| **PROVISION**  | An older name for DEONTIC                                         | [DEONTIC](/l4/reference/regulative/DEONTIC.md)             |
| **EVENT**      | Something a party did at a time, for traces: `EVENT party action` | [EVENT](/l4/reference/regulative/EVENT.md)                 |

## Constants and constructors

| Name                                              | Meaning                                                                | Reference                                |
| ------------------------------------------------- | ---------------------------------------------------------------------- | ---------------------------------------- |
| **TRUE** / **FALSE**                              | The two BOOLEAN values                                                 | [Types](/l4/reference/types.md)                 |
| **JUST** / **NOTHING**                            | A present or a missing MAYBE value                                     | [Types](/l4/reference/types.md)                 |
| **LEFT** / **RIGHT**                              | The two sides of an EITHER                                             | [Types](/l4/reference/types.md)                 |
| **EMPTY**                                         | The empty list                                                         | [Types](/l4/reference/types.md)                 |
| **TODAY**, **NOW**, **CURRENTTIME**, **TIMEZONE** | The current date, moment and time of day, and the document's time zone | [Built-in functions](/l4/reference/builtins.md) |

## Control flow

| Keyword                          | Meaning                                                                              | Reference                            |
| -------------------------------- | ------------------------------------------------------------------------------------ | ------------------------------------ |
| **IF** ... **THEN** ... **ELSE** | Chooses between two values                                                           | [IF](/l4/reference/control-flow/IF.md)             |
| **BRANCH**                       | A multi-way conditional: a list of `IF ... THEN` lines and an `OTHERWISE`            | [BRANCH](/l4/reference/control-flow/BRANCH.md)     |
| **CONSIDER**                     | Pattern matching: picks a branch by the shape of a value                             | [CONSIDER](/l4/reference/control-flow/CONSIDER.md) |
| **WHEN**                         | One branch of a CONSIDER                                                             | [CONSIDER](/l4/reference/control-flow/CONSIDER.md) |
| **OTHERWISE**                    | The branch for every remaining case, in CONSIDER and BRANCH                          | [CONSIDER](/l4/reference/control-flow/CONSIDER.md) |
| **EXACTLY**                      | In a pattern, matches a given value; in a regulative rule, matches an action exactly | [CONSIDER](/l4/reference/control-flow/CONSIDER.md) |
| **REFUSE**                       | Declines to answer, and gives the author's reason: `REFUSE "reason"`                 | [REFUSE](/l4/reference/control-flow/REFUSE.md)     |
| **REFUSED**                      | Reserved for `#ASSERT REFUSED`; a name spelled `REFUSED` is written in backticks     | [REFUSE](/l4/reference/control-flow/REFUSE.md)     |

## Operators

| Operator                         | Meaning                                                     | Reference                                      |
| -------------------------------- | ----------------------------------------------------------- | ---------------------------------------------- |
| **AND**, `&&`, `...`             | Both are true                                               | [AND, OR, NOT, IMPLIES](/l4/reference/operators/logical.md)  |
| **OR**, `\|\|`, `..`             | At least one is true                                        | [AND, OR, NOT, IMPLIES](/l4/reference/operators/logical.md)  |
| **NOT**                          | Negation                                                    | [AND, OR, NOT, IMPLIES](/l4/reference/operators/logical.md)  |
| **IMPLIES**, `=>`                | If the first is true, so is the second                      | [AND, OR, NOT, IMPLIES](/l4/reference/operators/logical.md)  |
| **UNLESS**                       | `a UNLESS b` is `a AND NOT b`, binding more loosely than OR | [UNLESS](/l4/reference/operators/UNLESS.md)                  |
| **PLUS**, `+`                    | Addition; also adds days to a date                          | [Arithmetic](/l4/reference/operators/arithmetic.md)   |
| **MINUS**, `-`                   | Subtraction                                                 | [Arithmetic](/l4/reference/operators/arithmetic.md)   |
| **TIMES**, `*`                   | Multiplication                                              | [Arithmetic](/l4/reference/operators/arithmetic.md)   |
| **DIVIDED BY**, `/`              | Division                                                    | [Arithmetic](/l4/reference/operators/arithmetic.md)   |
| **MODULO**                       | Remainder of a division of whole numbers                    | [Arithmetic](/l4/reference/operators/arithmetic.md)   |
| `%`                              | Percent after a number: `5%` is 0.05                        | [Arithmetic](/l4/reference/operators/arithmetic.md)   |
| **EQUALS**, `=`                  | Equality                                                    | [Comparisons](/l4/reference/operators/comparisons.md) |
| **GREATER THAN**, **ABOVE**, `>` | Greater than                                                | [Comparisons](/l4/reference/operators/comparisons.md) |
| **LESS THAN**, **BELOW**, `<`    | Less than                                                   | [Comparisons](/l4/reference/operators/comparisons.md) |
| **AT LEAST**, `>=`               | Greater than or equal                                       | [Comparisons](/l4/reference/operators/comparisons.md) |
| **AT MOST**, `<=`                | Less than or equal                                          | [Comparisons](/l4/reference/operators/comparisons.md) |
| **FOLLOWED BY**                  | Puts an item in front of a list                             | [Operators](/l4/reference/operators.md)               |
| **CONCAT**                       | Joins strings: `CONCAT "a", "b"`                            | [Built-in functions](/l4/reference/builtins.md#text)  |
| `'s`                             | Reads a field: `person's age`                               | [Syntax and directives](/l4/reference/syntax.md)      |

## Regulative rules

| Keyword              | Meaning                                                                               | Reference                                                         |
| -------------------- | ------------------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| **PARTY**            | Who the rule applies to                                                               | [PARTY, MUST, MAY, SHANT](/l4/reference/regulative/PARTY.md)                    |
| **MUST**             | Obligation                                                                            | [PARTY, MUST, MAY, SHANT](/l4/reference/regulative/PARTY.md)                    |
| **MAY**              | Permission                                                                            | [PARTY, MUST, MAY, SHANT](/l4/reference/regulative/PARTY.md)                    |
| **SHANT / MUST NOT** | Prohibition; the two are synonyms                                                     | [PARTY, MUST, MAY, SHANT](/l4/reference/regulative/PARTY.md#shant-and-must-not) |
| **DO**               | An action neither required nor forbidden; without HENCE and LEST it behaves like MUST | [PARTY, MUST, MAY, SHANT](/l4/reference/regulative/PARTY.md)                    |
| **PROVIDED**         | A condition the action must meet                                                      | [Deadlines and consequences](/l4/reference/regulative/consequences.md)          |
| **WITHIN**           | The deadline, counted from when the rule starts                                       | [Deadlines and consequences](/l4/reference/regulative/consequences.md)          |
| **HENCE**            | What follows when the rule is complied with                                           | [Deadlines and consequences](/l4/reference/regulative/consequences.md)          |
| **LEST**             | What follows when it is not                                                           | [Deadlines and consequences](/l4/reference/regulative/consequences.md)          |
| **FULFILLED**        | The end state: nothing more is owed                                                   | [Deadlines and consequences](/l4/reference/regulative/consequences.md)          |
| **BREACH**           | The end state: a rule was broken; `BREACH BY party BECAUSE reason`                    | [Deadlines and consequences](/l4/reference/regulative/consequences.md)          |
| **BY**               | Names the party in `BREACH BY`                                                        | [Deadlines and consequences](/l4/reference/regulative/consequences.md)          |
| **RAND**             | Both rules apply at once                                                              | [Deadlines and consequences](/l4/reference/regulative/consequences.md)          |
| **ROR**              | Either rule may be complied with                                                      | [Deadlines and consequences](/l4/reference/regulative/consequences.md)          |
| **BECAUSE**          | The reason given for a breach                                                         | [BECAUSE](/l4/reference/regulative/BECAUSE.md)                                  |
| **DOES**             | In a trace event: `PARTY Seller DOES deliver AT 3`                                    | [EVENT](/l4/reference/regulative/EVENT.md)                                      |
| **AT**               | The time of an event, or the start time of a `#TRACE`                                 | [EVENT](/l4/reference/regulative/EVENT.md)                                      |

## Files and modules

| Keyword         | Meaning                                             | Reference                                        |
| --------------- | --------------------------------------------------- | ------------------------------------------------ |
| **IMPORT**      | Uses the definitions of another file or a library   | [Standard libraries](/l4/reference/libraries.md#import) |
| **TIMEZONE IS** | Sets the document's time zone, an IANA name         | [timezone](/l4/reference/libraries/timezone.md)                |
| `§`             | Starts a section of the document; `§§` a subsection | [Syntax and directives](/l4/reference/syntax.md)        |

## Directives

| Directive         | Meaning                                                                                                   | Reference                                                         |
| ----------------- | --------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| `#EVAL`           | Evaluates an expression and shows the result                                                              | [Syntax and directives](/l4/reference/syntax.md#directives)              |
| `#EVALTRACE`      | Evaluates and shows each step                                                                             | [Syntax and directives](/l4/reference/syntax.md#directives)              |
| `#CHECK`          | Shows the type of an expression without evaluating it                                                     | [Syntax and directives](/l4/reference/syntax.md#directives)              |
| `#ASSERT`         | Reports whether the expression is TRUE: FALSE is an error, and a refusal a warning                        | [What `#ASSERT` reports](/l4/reference/syntax.md#what-assert-reports)    |
| `#ASSERT REFUSED` | Reports an error unless the expression refuses to answer, with the reason after `BECAUSE` if there is one | [REFUSE](/l4/reference/control-flow/REFUSE.md#testing-a-refusal-assert-refused) |
| `#TRACE`          | Runs a regulative rule against events: `#TRACE rule AT 0 WITH ...`; `STARTING AT` is the same as `AT`     | [Syntax and directives](/l4/reference/syntax.md#directives)              |

## Annotations

| Annotation             | Meaning                                                           | Reference                                             |
| ---------------------- | ----------------------------------------------------------------- | ----------------------------------------------------- |
| `@desc`                | A description of a definition, shown in tools and APIs            | [Syntax and directives](/l4/reference/syntax.md#annotations) |
| `@nlg`, `[...]`        | Wording used when a rule is put into natural language             | [@nlg](/l4/reference/syntax/nlg.md)                                 |
| `@nlg:he`              | A wording in a named language; a rule can carry one per language  | [@nlg](/l4/reference/syntax/nlg.md#several-languages)               |
| `@lang he`             | The language an untagged `@nlg` in the module is written in       | [@nlg](/l4/reference/syntax/nlg.md#several-languages)               |
| `@ref`, `<<...>>`      | A reference to the legal source                                   | [Syntax and directives](/l4/reference/syntax.md#annotations) |
| `@ref-src`, `@ref-map` | Where references are looked up                                    | [Syntax and directives](/l4/reference/syntax.md#annotations) |
| `@export`              | Publishes a function as an API endpoint when the file is deployed | [Syntax and directives](/l4/reference/syntax.md#annotations) |
| `@export default`      | The function a deployment calls when no function is named         | [Syntax and directives](/l4/reference/syntax.md#annotations) |

## Symbols

| Symbol             | Meaning                                                    | Reference                                 |
| ------------------ | ---------------------------------------------------------- | ----------------------------------------- |
| `--`, `//`         | A comment to the end of the line                           | [Syntax and directives](/l4/reference/syntax.md) |
| `{- -}`, `/* */`   | A comment over several lines                               | [Syntax and directives](/l4/reference/syntax.md) |
| `` ` ` ``          | Backticks around a name with spaces: `` `the applicant` `` | [Syntax and directives](/l4/reference/syntax.md) |
| `"..."`            | A STRING                                                   | [Syntax and directives](/l4/reference/syntax.md) |
| `^`                | Ditto: repeats the word above                              | [Syntax and directives](/l4/reference/syntax.md) |
| `( )`              | Groups an expression                                       | [Syntax and directives](/l4/reference/syntax.md) |
| `,`                | Separates list items and arguments after OF                | [Syntax and directives](/l4/reference/syntax.md) |
| `` `_` ``          | In a pattern, matches anything                             | [CONSIDER](/l4/reference/control-flow/CONSIDER.md)      |
| `_` between digits | Groups digits: `100_000` is 100000                         | [Syntax and directives](/l4/reference/syntax.md) |

## Terms

| Term                    | Meaning                                                                                                        | Reference                                                |
| ----------------------- | -------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------- |
| **Regulative rule**     | A rule about behaviour: who MUST, MAY or SHANT do what, by when, and what follows                              | [Regulative rules](/l4/reference/regulative.md)                 |
| **Constitutive rule**   | A rule that defines, classifies or calculates, written with DECIDE or MEANS                                    | [Functions](/l4/reference/functions.md)                         |
| **Deontic modality**    | Obligation (MUST), permission (MAY) or prohibition (SHANT/MUST NOT)                                            | [PARTY, MUST, MAY, SHANT](/l4/reference/regulative/PARTY.md)           |
| **Defeasibility**       | A general rule giving way to an exception ("unless", "notwithstanding"), written with UNLESS or branches       | [UNLESS](/l4/reference/operators/UNLESS.md)                            |
| **Mixfix**              | A name with inputs between its words, such as ``borrower `pays` lender``, so a call reads like a sentence      | [Functions](/l4/reference/functions.md)                         |
| **Isomorphism**         | Code that follows the structure of the legal text, clause for clause                                           | [Concepts](/l4/concepts.md)                        |
| **Residual obligation** | What is still owed after some events: the DEONTIC value a `#TRACE` gives                                       | [DEONTIC](/l4/reference/regulative/DEONTIC.md)                         |
| **Reparation**          | A further obligation that follows a breach, written in a LEST                                                  | [Deadlines and consequences](/l4/reference/regulative/consequences.md) |
| **Clauses**             | A rule written as one line per case, each with values to match in place of its inputs; the first match applies | [DECIDE](/l4/reference/functions/DECIDE.md#several-clauses)            |
| **Exhaustiveness**      | Whether the branches of a CONSIDER, or the clauses of a rule, cover every case; L4 warns when they do not      | [CONSIDER](/l4/reference/control-flow/CONSIDER.md)                     |
