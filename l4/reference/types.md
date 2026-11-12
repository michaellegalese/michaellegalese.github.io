# Types

Every value in L4 has a type, and the type checker makes sure each rule is given and returns values of the right types.
L4 has a few built-in types, and `DECLARE` makes new ones: records, enumerations and synonyms.
Types are usually inferred, but writing them in `GIVEN` and `GIVETH` makes rules easier to read.

## Pages

- [DECLARE](/l4/reference/types/DECLARE.md): records, enumerations, type synonyms, parameterised types, `A`/`AN` and type expressions
- [ASSUME](/l4/reference/types/ASSUME.md): a name with a type and no definition
- [FOR ALL](/l4/reference/types/for-all.md): type variables in the type of an `ASSUME`
- [Coercions](/l4/reference/types/coercions.md): converting between types (`TOSTRING`, `TONUMBER`, `TODATE`, `AS STRING` and others)

## Built-in types

| Type       | Values                                                                                                       |
| ---------- | ------------------------------------------------------------------------------------------------------------ |
| `NUMBER`   | exact numbers, whole or fractional: `42`, `-17`, `3.14`, `5.4%` (= `0.054`), `100_000`                       |
| `STRING`   | text in double quotes: `"Hello"`; `\"`, `\\` and `\n` are escapes                                            |
| `BOOLEAN`  | `TRUE` and `FALSE`                                                                                           |
| `DATE`     | a calendar date, made with `Date 15 1 2025` from [daydate](/l4/reference/libraries/daydate.md), or parsed with `TODATE` |
| `TIME`     | a time of day, without a date or time zone; see [time](/l4/reference/libraries/time.md)                                 |
| `DATETIME` | a point in time with a time zone; see [datetime](/l4/reference/libraries/datetime.md)                                   |

- Arithmetic on `NUMBER` is exact: `10 / 4` is `2.5`, not a rounded value.
- In a number, underscores between digits are ignored: `100_000` is `100000`. A number must start with a digit and must not end with an underscore.
- `TRUE` and `FALSE` are written in capitals.

## Lists, optional values and choices

These built-in types take another type as a parameter.

| Type         | Values                                                        | Example file                           |
| ------------ | ------------------------------------------------------------- | -------------------------------------- |
| `LIST OF a`  | `LIST 1, 2, 3`, `EMPTY`, `1 FOLLOWED BY EMPTY`                | 

```l4-file
-- Example: List type usage
-- Demonstrates list literals and cons operator

-- List literal syntax
`first list` MEANS LIST 1, 2, 3

-- Cons operator (FOLLOWED BY)
`second list` MEANS 1 FOLLOWED BY 2 FOLLOWED BY 3 FOLLOWED BY EMPTY

#EVAL `first list`
#EVAL `second list`
```

     |
| `MAYBE a`    | `JUST 42` when there is a value, `NOTHING` when there is none | 

```l4-file
-- Example: Maybe type usage
-- Demonstrates optional values with JUST and NOTHING

`value is present` MEANS JUST OF 42
`value is absent` MEANS NOTHING

`get the value or default to zero` MEANS
  CONSIDER `value is present`
  WHEN JUST OF x THEN x
  WHEN NOTHING THEN 0

#EVAL `get the value or default to zero`
```

   |
| `EITHER a b` | `LEFT "error"` or `RIGHT 42`                                  | 

```l4-file
-- Example: Either type usage
-- Either represents a choice: LEFT for errors, RIGHT for success values

-- Declare a result with explicit EITHER type
ASSUME `api result` IS AN EITHER STRING NUMBER

-- Create specific Either values
`error case` MEANS LEFT OF "Error occurred"
`success case` MEANS RIGHT OF 42

-- Pattern match to handle both cases
GIVEN result IS AN EITHER STRING NUMBER
`handle the result` result MEANS
  CONSIDER result
  WHEN LEFT OF err THEN CONCAT "Error: ", err
  WHEN RIGHT OF val THEN CONCAT "Success: ", TOSTRING val

#EVAL `handle the result` `error case`
#EVAL `handle the result` `success case`
```

 |

L4 has no null: a value that may be missing has a `MAYBE` type, and [CONSIDER](/l4/reference/control-flow/CONSIDER.md) handles both cases.
Functions on these types, and the `PAIR` and `Dictionary` types, are in the [prelude](/l4/reference/libraries/prelude.md).

## Events and regulative rules

These built-in types take the type of the parties and the type of the actions as their two parameters.

| Type                   | Values                                                                                                                           |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `DEONTIC party action` | a regulative rule: an obligation, permission or prohibition, or `FULFILLED` or `BREACH`; see [DEONTIC](/l4/reference/regulative/DEONTIC.md) |
| `EVENT party action`   | that a party did an action at a time (a `NUMBER`): `EVENT Seller delivery 2`; see [EVENT](/l4/reference/regulative/EVENT.md)                |

## Records and enumerations

A record has named fields; an enumeration has a fixed list of values, each of which can carry fields. Both are made with [DECLARE](/l4/reference/types/DECLARE.md).
Examples: 

```l4-file
-- Example: Record type declaration
-- Demonstrates product types with named fields

DECLARE Person
  HAS `full name` IS A STRING
      age         IS A NUMBER

-- Example: Creating a person record
`the applicant` MEANS Person WITH
  `full name` IS "John Doe"
  age         IS 30

#EVAL `the applicant`'s `full name`
#EVAL `the applicant`'s age
```

, 

```l4-file
-- Example: Enum type declaration
-- Demonstrates sum types with named constructors

DECLARE Color IS ONE OF
  Red
  Green
  Blue

-- Example usage
`my favorite color` MEANS Red

`name of the color` MEANS
  CONSIDER `my favorite color`
  WHEN Red   THEN "red"
  WHEN Green THEN "green"
  WHEN Blue  THEN "blue"

#EVAL `name of the color`
```

.

## Functions

A function is a value too. Its type is written `FUNCTION FROM Type1 AND Type2 TO Result`, so a function can be an input of another function.
Example: 

```l4-file
-- Example: Function type
-- Demonstrates function type signature

GIVEN x IS A NUMBER
      y IS A NUMBER
GIVETH A NUMBER
add x y MEANS x PLUS y

#EVAL add 5 3
```

.

## Type parameters and inference

A function or a type can work for any type, with a type parameter (`GIVEN a IS A TYPE`; see [GIVEN and GIVETH](/l4/reference/functions/GIVEN.md#type-parameters)).
Example: 

```l4-file
-- Example: Polymorphic function
-- Demonstrates generic types that work with any type

GIVEN a IS A TYPE
      list IS A LIST OF a
GIVETH A NUMBER
`length of list` list MEANS go 0 list
  WHERE
    go acc l MEANS
      CONSIDER l
      WHEN EMPTY THEN acc
      WHEN x FOLLOWED BY xs THEN go (acc + 1) xs

#EVAL `length of list` (LIST 1, 2, 3)
#EVAL `length of list` (LIST "a", "b", "c")
```

.

When `GIVETH` or an input's type is left out, L4 infers it.
Example: 

```l4-file

GIVEN x IS A NUMBER
      y IS A NUMBER
`add two numbers` x y MEANS x PLUS y
```

.

## Comparison with other languages

| L4                    | Haskell              | Other languages           |
| --------------------- | -------------------- | ------------------------- |
| `DECLARE T HAS ...`   | `data T = T { ... }` | struct, class, record     |
| `DECLARE T IS ONE OF` | `data T = A \| B`    | enum, union, sealed class |
| `GIVEN … GIVETH`      | `a -> b`             | function signature        |
| `MAYBE T`             | `Maybe T`            | Optional, nullable        |
| `LIST OF T`           | `[T]`                | array, list               |
| `CONSIDER`            | `case … of`          | switch, match             |
| `BRANCH`              | guards               | if-else-if chain          |
| `WHERE`, `LET … IN`   | `where`, `let … in`  | local variables           |
| `'s`                  | record field         | `.field`                  |

How the type system works, formally: [Type theory](/l4/concepts/type-system/type-theory.md). Why types matter for legal rules: [Types as legal categories](/l4/concepts/type-system/algebraic-types.md).
