# Syntax and directives

This page covers how an L4 file is written: layout, comments, names, literals, annotations, directives and the special forms that make rules read like legal text.
For how to write rules that read well, see [Writing in the L4 style](/l4/concepts/language-design/principles.md#writing-in-the-l4-style).

## Layout

L4 is layout-sensitive: indentation, not braces or semicolons, shows what belongs to what.
The lines of a block are indented further than the line that opens it, and the items of a block (fields, inputs, branches) start in the same column.
Spaces only; a tab is not a substitute.

**Example:** 

```l4-file
-- Example: Layout rules (indentation-based grouping)
-- Demonstrates how indentation defines code blocks

DECLARE Person
  HAS name IS A STRING    -- Indented: part of declaration
      age IS A NUMBER     -- Same level: another field
      
john MEANS Person WITH
  name IS "John Doe"
  age  IS 30

#EVAL john's name
```



In a long expression, indentation also groups the operators; see [AND, OR, NOT, IMPLIES: Several lines](/l4/reference/operators/logical.md#several-lines).

## Comments

- `--` or `//` starts a comment that runs to the end of the line.
- `{- … -}` and `/* … */` enclose a comment that can span several lines.

**Example:** 

```l4-file
-- Comments

-- A line comment starts with two dashes
// or with two slashes
`the age limit` MEANS 18  -- and can follow code on a line

{- A block comment
   can span several lines -}

/* So can this
   one */

#EVAL `the age limit`
```



## Identifiers

- A plain identifier starts with a letter and continues with letters, digits and underscores: `age`, `income_band`.
- A quoted identifier is written in backquotes and can contain spaces and punctuation: `` `the applicant` ``, `` `Section 2(a)` ``. Prefer these: they let a rule read like the legal text.
- Keywords (`DECIDE`, `GIVEN`, `IF`, …) are written in capitals. Identifiers are case-sensitive: `` `the rate` `` and `` `The rate` `` are different names.

**Example:** 

```l4-file
-- Identifiers: plain and quoted

-- A plain identifier: letters, digits and underscores, starting with a letter
ASSUME age IS A NUMBER
ASSUME income_band IS A STRING

-- A quoted identifier, in backquotes, can contain spaces and punctuation
ASSUME `total tax` IS A NUMBER
ASSUME `Section 2(a)` IS A STRING

-- Identifiers are case-sensitive: these are two different names
`the rate` MEANS 1
`The rate` MEANS 2

#EVAL `the rate`
#EVAL `The rate`
```



## Literals

| Kind    | Examples                                                     |
| ------- | ------------------------------------------------------------ |
| Number  | `42`, `-17`, `3.14`, `5%` (= `0.05`), `100_000`              |
| Text    | `"hello"`, with `\"`, `\\` and `\n` as escapes               |
| Boolean | `TRUE`, `FALSE`                                              |
| List    | `LIST 1, 2, 3`, `EMPTY`, `1 FOLLOWED BY 2 FOLLOWED BY EMPTY` |

Underscores between the digits of a number are ignored. A number must start with a digit and must not end with an underscore.
There is no date literal; see [Types](/l4/reference/types.md#built-in-types).

## IMPORT

`IMPORT name` at the top of a file, before the first `§` section, makes the definitions of a library or another L4 file available. An `IMPORT` below a section heading is ignored. See [Standard libraries: IMPORT](/l4/reference/libraries.md#import).

## Annotations

Annotations attach information to the definition that follows them, or to the line they end.

### @desc

`@desc text` describes a rule, a type or an input. It goes on the line above a definition, or at the end of an input's line. Deployed functions show it to their callers.

```l4
@desc The applicant's age in years
ASSUME `the applicant's age` IS A NUMBER
```

### @nlg

`@nlg` gives a definition its wording in natural language, with `%name%` slots for its inputs.

```l4
GIVEN x IS A NUMBER, y IS A NUMBER
GIVETH A NUMBER
@nlg the greater of %x% and %y%
`the greater of` x y MEANS IF x >= y THEN x ELSE y
```

See [@nlg](/l4/reference/syntax/nlg.md).

### @ref

`@ref` cites a legal source: the provision a rule, a type or an input comes from.

- **Line form:** `@ref` and the citation, on a line of its own.
- **Inline form:** the citation between `<<` and `>>`, which can sit in the middle of a line.

#### What a `@ref` cites

A `@ref` cites what comes **after** it, so write it on the line above the thing it cites:

```l4
@ref Section 4(1)(a) of the Act
GIVEN age IS A NUMBER
      resident IS A BOOLEAN
GIVETH A BOOLEAN
`is eligible` MEANS
  age >= 18
  AND <<Section 4(1)(b)>> resident
```

The `@ref` cites the rule `is eligible`; the `<<…>>` cites `resident`, the expression that follows it.
A `@ref` is held by the first construct that starts after it: a rule, a type, a section heading, an input, or an expression inside a rule.
Blank lines and comments between a `@ref` and that construct make no difference.
A citation before the first operand of a chain of `AND`, `OR` or arithmetic is held by the whole chain, because the chain is the outermost expression that starts after it; to cite one operand, put the citation just before that operand inside the chain, as with `resident` above.

**Several citations.** Write them one above the other, each on a line of its own.
The construct below keeps all of them, in the order written.

```l4
@ref Dog Act 058 of 1976 (WA)
@ref url https://www.legislation.wa.gov.au/legislation/statutes.nsf/law_a230.html
§ `Dogs Act - 33D.1`
```

Where only one citation per construct is wanted, the nearest is used: the one written last, just above the construct.
In the editor, hovering over a citation shows it as a link.

**`@ref-map name url`** maps a citation to a link: a `@ref` whose text is `name` links to `url`.

#### Two warnings

Both are warnings, so `l4 check` still succeeds, and the VS Code extension marks the `@ref` itself.
The browser-based editor does not show them.

**A `@ref` that follows code on its line.**
A `@ref` written at the end of a line, after the code it means to cite, cannot cite that code, because it cites what comes after it.
It does not cite the next construct either: it is attached to nothing, so the next construct's citation, in the IDE and in every export, is not this one.
The warning names the code it trails:

> This @ref (`1981/61 sec. 2`) follows code on its line, but a @ref refers to what follows it, so this one is attached to nothing. Put it on the line above `GIVETH A BOOLEAN`.

The fix is to move it to the line above that code.
An inline `<<…>>` is warned about only when it ends the line, because in the middle of a line it cites the word after it, which is what it is for.

**A `@ref` with nothing after it.**
A `@ref` after the last construct of the file has nothing to attach to:

> This @ref (`Section 4(1)(a) of the Act`) is at the end of the file, so there is nothing after it to attach to.

#### Writing a literal `>>`

The inline form ends at `>>`, so a citation that contains those two characters would otherwise stop halfway.
Put a backslash before the angle bracket:

| you write                | you get             |
| ------------------------ | ------------------- |
| `<<see s.5 \>> onward>>` | `see s.5 >> onward` |
| `<<\\>>`                 | `\`                 |

**A single `>` needs nothing.**
`<<s.5 > s.3>>` is one citation: only the doubled `>>` closes it, so a lone `>` is ordinary text.

**To end a citation with a backslash, write `\\`.**
A backslash immediately before `>>` is an escape, so `\>` is a literal `>` and the citation goes on.
Any other character after a backslash is left alone, so `\q` is a backslash followed by a `q`.

This is the same idea as the `\%` and `\]` escapes in `@nlg`, but the set of characters is deliberately different.
`%` and `]` are ordinary text in a citation, and `>` is ordinary text in an `@nlg` annotation.
Each annotation escapes only what would otherwise be read as punctuation in that annotation.

### @export

`@export` marks a function to be published as an endpoint when the file is deployed. The rest of the line is the function's description. Functions without `@export` stay internal.

```l4
@export Check whether a person is old enough to apply
GIVEN age IS A NUMBER @desc The person's age in years
GIVETH A BOOLEAN
DECIDE `is old enough` IF age >= 18
```

`@export default` marks the file's main entry point among its exports.
A function written as [several clauses](/l4/reference/functions/DECIDE.md#several-clauses) needs a `GIVEN` to be exported, and a function-typed input cannot be exported ([ASSUME](/l4/reference/types/ASSUME.md#function-typed-inputs-are-not-supported-for-export)).
How to deploy: [Deploy your rules](/l4/tutorials/deploying-rules/exporting-rules-for-deployment.md).

**Example:** 

```l4-file
-- Annotations: @desc, @nlg, @ref, @export

@desc The applicant's age in years
ASSUME `the applicant's age` IS A NUMBER

-- @nlg at the end of a definition's line gives its wording, with %name% slots
GIVEN x IS A NUMBER, y IS A NUMBER
GIVETH A NUMBER
`the greater of` x y @nlg the greater of %x% and %y%
  MEANS IF x >= y THEN x ELSE y

-- A @ref cites what comes after it
@ref Section 4(1)(a) of the Act
@export Check whether a person is old enough to apply
GIVEN age IS A NUMBER @desc The person's age in years
GIVETH A BOOLEAN
DECIDE `is old enough` IF age >= 18

#EVAL `the greater of` 3 7
#EVAL `is old enough` 20

-- A literal percent sign or close bracket in an `@nlg` annotation is written
-- with a backslash in front of it. Here the rate range `10%and%20` would
-- otherwise read `and` as a parameter name, silently.
GIVEN amount IS A NUMBER
GIVETH A NUMBER
`the levy on` amount [a rate between 10\%and\%20 applied to %amount%]
MEANS amount TIMES 0.15

#EVAL `the levy on` 100

-- A citation containing a literal `>>` needs a backslash before the angle
-- bracket, or the inline `@ref` form would end halfway through it.
GIVEN claimant IS A BOOLEAN
GIVETH A BOOLEAN
`is eligible` claimant MEANS
  <<Section 4(1)(a) \>> as amended>>
  claimant

#EVAL `is eligible` TRUE

-- An `@nlg` may carry a language subtag, and one rule may carry a
-- rendering per language. `l4 nlg --lang he` and `l4 render --lang he` select
-- the Hebrew one; with no flag, or for a rule that has none in that language,
-- the default rendering is used.
GIVEN amount IS A NUMBER
GIVETH A BOOLEAN
DECIDE `is a large amount` @nlg:en the amount %amount% is large
                           @nlg:he %amount% עולה על הסף
  IF amount GREATER THAN 100

#EVAL `is a large amount` 150
```



## Directives

Directives are instructions to the tools, written at the top level of a file. `l4 run` and the editor carry them out; `l4 check` only type-checks them.

| Directive         | What it does                                                                                                                                                                             |
| ----------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `#EVAL`           | evaluates an expression and shows the result                                                                                                                                             |
| `#EVALTRACE`      | evaluates an expression and also shows the steps that led to the result (`l4 trace` draws them)                                                                                          |
| `#CHECK`          | type-checks an expression and shows its type, without evaluating it                                                                                                                      |
| `#ASSERT`         | evaluates an expression and reports whether it is TRUE ([what it reports](#what-assert-reports))                                                                                         |
| `#ASSERT REFUSED` | evaluates an expression and reports whether it refuses to answer, with the reason after `BECAUSE` if there is one ([REFUSE](/l4/reference/control-flow/REFUSE.md#testing-a-refusal-assert-refused)) |
| `#TRACE`          | runs a regulative rule against a list of events (see [Regulative rules](/l4/reference/regulative.md))                                                                                        |

```text
#EVAL expression
#EVALTRACE expression
#CHECK expression
#ASSERT expression
#ASSERT REFUSED expression BECAUSE "reason"
#TRACE rule AT start-time WITH
  PARTY party DOES action AT time
```

A directive's expression ends with its line. To continue it, start the next line with `#` and a space, followed by an operator:

```l4
GIVEN age IS A NUMBER
GIVETH A BOOLEAN
DECIDE `is an adult` IF age >= 18

#EVAL `is an adult` 21
#   AND `is an adult` 30
#CHECK `is an adult`
#ASSERT NOT `is an adult` 12
```

**Example:** 

```l4-file
-- Directives: #EVAL, #EVALTRACE, #CHECK, #ASSERT, #TRACE

GIVEN age IS A NUMBER
GIVETH A BOOLEAN
DECIDE `is an adult` IF age >= 18

-- #EVAL evaluates an expression and shows the result
#EVAL `is an adult` 21

-- A directive continues on the next line when that line starts with "# "
-- and the expression goes on with an operator
#EVAL `is an adult` 21
#   AND `is an adult` 30

-- #EVALTRACE also shows how the result was reached
#EVALTRACE `is an adult` 15

-- #CHECK type-checks an expression and shows its type, without evaluating it
#CHECK `is an adult`

-- #ASSERT checks that an expression is TRUE
#ASSERT `is an adult` 40
#ASSERT NOT `is an adult` 12

-- #TRACE runs a regulative rule against events
DECLARE Person IS ONE OF Seller
DECLARE Action IS ONE OF delivery

GIVETH A DEONTIC Person Action
`delivery duty` MEANS
  PARTY Seller
  MUST delivery
  WITHIN 3

#TRACE `delivery duty` AT 0 WITH
  PARTY Seller DOES delivery AT 2
```



### What `#ASSERT` reports

An `#ASSERT` has one of four outcomes:

| When the expression…                                                                                         | It prints                                          | The editor shows           | `l4 run` exit code | In `l4 run --json`                                 |
| ------------------------------------------------------------------------------------------------------------ | -------------------------------------------------- | -------------------------- | ------------------ | -------------------------------------------------- |
| is TRUE                                                                                                      | `assertion satisfied`                              | information                | not affected       | `"value": true`                                    |
| is FALSE                                                                                                     | `assertion failed`                                 | an error                   | not affected       | `"value": false`                                   |
| cannot be evaluated: a division by zero, a `CONSIDER` with no matching branch, a term that is only `ASSUME`d | `assertion could not be evaluated:` and the reason | an error, with the reason  | non-zero           | `"value": null`, with the reason under `"error"`   |
| reaches a `REFUSE`: the rules decline to answer                                                              | `assertion refused:` and the reason                | a warning, with the reason | not affected       | `"value": null`, with the reason under `"refused"` |

In `--json` the result's `"kind"` is `"assertion"` in all four.

An assertion that cannot be evaluated is not a verdict, so `#ASSERT P` and `#ASSERT NOT P` then report the same thing. Asserting an `ASSUME`d `BOOLEAN` directly, as in `#ASSERT b`, is one such case: `b` has no value to check.
[Testing your rules](/l4/tutorials/getting-started/testing-your-rules.md#what-an-error-looks-like) shows an example.

A refused assertion is not a verdict either, and `l4 run` still exits `0`, so a CI job that looks for failures looks for `assertion refused` as well as `assertion failed`.

To assert that an expression refuses, use `#ASSERT REFUSED`; [Testing a refusal](/l4/reference/control-flow/REFUSE.md#testing-a-refusal-assert-refused) lists what it reports.

## Sections

`§` starts a section, like a part or section of a statute; `§§` starts a subsection, `§§§` a sub-subsection, and so on. The heading is the name after the marks.

```l4
§ `Part VII`

§§ `Subsection 2`
`age of majority` MEANS 18

§§ `Subsection 3`
`age of majority` MEANS 21

GIVEN age IS A NUMBER
GIVETH A BOOLEAN
DECIDE `is an adult under subsection 2` IF
    age >= `Part VII`'s `Subsection 2`'s `age of majority`
```

Definitions in different sections can share a name. Where the name alone would be ambiguous, qualify it with the section's name and `'s`, as above.
A section can have a shorter alias: ``§ `Definitions for Part VII` AKA defs``, then `` defs's `taxable income` `` (or `` defs.`taxable income` ``).

**Example:** 

```l4-file
-- Sections (§) group definitions, like the parts of a statute

§ `Eligibility Rules`

`the minimum age` MEANS 18

§§ `Age Requirements`

GIVEN age IS A NUMBER
GIVETH A BOOLEAN
`is old enough` age MEANS age >= `the minimum age`

§§ `Citizenship Requirements`

GIVEN `is a citizen` IS A BOOLEAN
GIVETH A BOOLEAN
`meets citizenship requirement` `is a citizen` MEANS `is a citizen`

#EVAL `is old enough` 21
#EVAL `meets citizenship requirement` TRUE
```



## Genitive

`'s` reads a field of a record, as in English: `` `the applicant`'s age ``. It can be chained: `` `the applicant`'s address's city ``.
``age OF `the applicant` `` means the same.

**Example:** 

```l4-file
-- Genitive: 's reads a field of a record, as in English

DECLARE Address
  HAS city IS A STRING

DECLARE Person
  HAS `full name` IS A STRING
      age         IS A NUMBER
      address     IS AN Address

DECIDE `the applicant` IS Person WITH
  `full name` IS "Kim Tan"
  age         IS 30
  address     IS Address WITH city IS "Singapore"

-- A field is also a function of its record, so these two mean the same
#EVAL `the applicant`'s age
#EVAL age OF `the applicant`

-- 's can be chained
#EVAL `the applicant`'s address's city
```



## OF

`OF` introduces arguments separated by commas: `add OF 3, 4` means the same as `add 3 4`.
It also appears in types (`LIST OF NUMBER`, `Box OF NUMBER`), in enumerations (`IS ONE OF`), when making a record (`Person OF "Kim", 30`) and in patterns (`WHEN JUST OF x`).

## Ditto

`^` repeats the word in the same position on the line above, one `^` per word. It keeps repetitive lines short.

**Example:** 

```l4-file
-- Example: Ditto (^) syntax
-- Copy tokens from previous line (one ^ per token)

eligible MEANS TRUE

john  MEANS eligible
mary  ^     ^        -- Copies "MEANS eligible"
alice ^     ^        -- Also copies "MEANS eligible"

#EVAL john
#EVAL mary
#EVAL alice
```



## Asyndetic AND and OR

`...` means `AND` and `..` means `OR`, as in lists of conditions in legal text. See [Operators: Asyndetic AND and OR](/l4/reference/operators.md#asyndetic-and-and-or).

## Related pages

- [@nlg](/l4/reference/syntax/nlg.md): a definition's wording, in one language or several
- [Glossary](/l4/reference/GLOSSARY.md): every keyword, with links
- [Functions](/l4/reference/functions.md)
- [Operators](/l4/reference/operators.md)
