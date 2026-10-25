> This page has moved to [/l4/reference/types/DECLARE](/l4/reference/types/DECLARE.md#a-and-an).

# DECLARE

`DECLARE` introduces a new type: a record, an enumeration, or a synonym for another type.

## Syntax

```text
DECLARE Name HAS field1 IS A Type1, field2 IS A Type2
DECLARE Name IS ONE OF Value1, Value2, Value3
DECLARE Name IS A Type
DECLARE Name OF a, b HAS ...
```

**Example files:** 

```l4-file
-- DECLARE keyword examples

-- Record Types (Product Types)

-- Basic record with multiple fields
DECLARE Person
  HAS
    `full name` IS A STRING
    age         IS A NUMBER

-- Record with single line syntax
DECLARE Point HAS x IS A NUMBER, y IS A NUMBER

-- Enum Types (Sum Types)

-- Simple enum with value constructors
DECLARE Colour IS ONE OF `bright red`, `forest green`, `ocean blue`

-- Enum with constructors that have fields
DECLARE Shape IS ONE OF
  Circle HAS radius IS A NUMBER
  Rectangle HAS width IS A NUMBER, height IS A NUMBER
  Point

-- Type Synonyms

DECLARE Age IS NUMBER
DECLARE `Person name` IS STRING

-- Parameterised Types

-- The type parameter written after the name ...
DECLARE Box OF a HAS contents IS AN a

-- ... or declared with GIVEN above the DECLARE
GIVEN a IS A TYPE, b IS A TYPE
DECLARE `Pair of values` HAS first IS AN a, second IS A b

-- Usage Examples

-- Create record instances
DECIDE `the applicant` IS Person WITH `full name` IS "John", age IS 30
DECIDE `the origin` IS Point WITH x IS 0, y IS 0

-- Use enum constructors
DECIDE `my favourite colour` IS `bright red`
DECIDE `my shape` IS Circle WITH radius IS 5

-- Use parameterised types
GIVETH A Box OF NUMBER
DECIDE `box with number` IS Box WITH contents IS 42
GIVETH A Box OF STRING
DECIDE `box with text` IS Box WITH contents IS "hello"
GIVETH A `Pair of values` OF NUMBER, STRING
DECIDE `a pair` IS `Pair of values` WITH first IS 1, second IS "one"

-- Evaluate examples
#EVAL `the applicant`'s `full name`
#EVAL `the applicant`'s age
#EVAL `my favourite colour`
#EVAL `box with text`'s contents
#EVAL `a pair`'s second
```

, 

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



## Records

A record has named fields. Write them after `HAS`, one per line or separated by commas:

```l4
DECLARE Person
  HAS
    name IS A STRING
    age IS A NUMBER

DECLARE Point HAS x IS A NUMBER, y IS A NUMBER
```

A field can also be written `name: STRING` or `name: A STRING`.

**Making a record.** Give every field by name with `WITH`, or give the values in the order the fields are declared, with `OF` and commas or with spaces:

```l4
DECLARE Person HAS
  name IS A STRING
  age IS A NUMBER

`the buyer` MEANS Person WITH name IS "Kim", age IS 30
`the seller` MEANS Person OF "Lee", 45
`the agent` MEANS Person "Jo" 28

#EVAL `the buyer`'s age
```

**Reading a field.** Use the genitive `'s`: `` `the buyer`'s age ``. See [Syntax and directives: Genitive](/l4/reference/syntax.md#genitive).

## Computed fields

A field followed by `MEANS` is computed from the record's other fields. It is read with `'s` like any other field, and it is not given when the record is made.

```l4
DECLARE Employee HAS
    `name`          IS A STRING
    `year of birth` IS A NUMBER
    `current year`  IS A NUMBER
    `age`           IS A NUMBER
        MEANS `current year` - `year of birth`
    `adult`         IS A BOOLEAN
        MEANS `age` >= 18

`the employee` MEANS Employee WITH
    `name`          IS "Lim"
    `year of birth` IS 1990
    `current year`  IS 2026

#EVAL `the employee`'s `adult`
```

- The body can use the record's other fields by name, including other computed fields, and call functions defined elsewhere.
- It can use `WHERE` and `LET … IN`.
- It takes no inputs of its own: there is no `GIVEN` for a computed field. Its only input is the record.
- Computed fields that depend on each other in a cycle are an error ("Circular dependency detected between computed fields").

Putting the stored fields first and the computed fields after them makes a declaration easier to read.

**Example file:** 

```l4-file
-- Computed Fields Example
-- Demonstrates "methods" on records: fields with MEANS clauses
-- whose values are automatically derived from other fields.

IMPORT prelude

-- ─────────────────────────────────────────────────────────
-- Basic computed fields (methods)
-- ─────────────────────────────────────────────────────────
-- Style: stored fields first, then computed/derived fields.

DECLARE Employee HAS
    -- stored fields (primary attributes)
    `name`               IS A STRING
    `date of birth`      IS A NUMBER
    `date of employment` IS A NUMBER
    `monthly salary`     IS A NUMBER
    `current year`       IS A NUMBER
    -- computed fields (derived attributes / methods)
    `age`                IS A NUMBER
        MEANS `current year` - `date of birth`
    `years of service`   IS A NUMBER
        MEANS `current year` - `date of employment`
    `senior`             IS A BOOLEAN
        MEANS `years of service` >= 10
    `eligible`           IS A BOOLEAN
        MEANS `senior`
          AND `monthly salary` <= 6000

-- Construction: only stored fields are supplied.
-- Computed fields are derived automatically.
veteran MEANS Employee WITH
    `name`               IS "Lim"
    `date of birth`      IS 1960
    `date of employment` IS 1990
    `monthly salary`     IS 3000
    `current year`       IS 2026

-- Access computed fields with 's, just like stored fields.
#EVAL veteran's `age`            -- 66
#EVAL veteran's `years of service` -- 36
#EVAL veteran's `senior`         -- TRUE
#EVAL veteran's `eligible`       -- TRUE

-- ─────────────────────────────────────────────────────────
-- Calling external functions from computed fields
-- ─────────────────────────────────────────────────────────
-- Multi-argument calls can use juxtaposition, OF, or mixfix:
--   `apply rate` `balance` `rate`         -- juxtaposition
--   `apply rate` OF `balance`, `rate`     -- OF (comma-separated)
--   `apply` `balance` `at rate` `rate`    -- mixfix (natural language)
-- All are equivalent. Choose whichever reads best in context.

GIVEN `base` IS A NUMBER
      `rate` IS A NUMBER
GIVETH A NUMBER
`apply rate` MEANS `base` * `rate` / 100

DECLARE Account HAS
    `balance`   IS A NUMBER
    `rate`      IS A NUMBER
    `interest`  IS A NUMBER
        MEANS `apply rate` `balance` `rate`
    `projected` IS A NUMBER
        MEANS `balance` + `interest`

savings MEANS Account WITH
    `balance` IS 10000
    `rate`    IS 5

#EVAL savings's `interest`   -- 500.0
#EVAL savings's `projected`  -- 10500.0

-- ─────────────────────────────────────────────────────────
-- WHERE and LET/IN inside computed fields
-- ─────────────────────────────────────────────────────────

GIVEN `x` IS A NUMBER
GIVETH A NUMBER
`square` MEANS `x` * `x`

DECLARE Shape HAS
    `width`   IS A NUMBER
    `height`  IS A NUMBER
    `area`    IS A NUMBER
        MEANS `width` * `height`
    `diag sq` IS A NUMBER
        MEANS `result`
          WHERE
            `w2` MEANS `square` `width`
            `h2` MEANS `square` `height`
            `result` MEANS `w2` + `h2`
    `scaled`  IS A NUMBER
        MEANS LET `factor` MEANS 2 IN `area` * `factor`

rect MEANS Shape WITH
    `width`  IS 3
    `height` IS 4

#EVAL rect's `area`     -- 12
#EVAL rect's `diag sq`  -- 25
#EVAL rect's `scaled`   -- 24
```



## Enumerations

`IS ONE OF` lists the values a type can have:

```l4
DECLARE Colour IS ONE OF Red, Green, Blue
```

A value can carry fields of its own:

```l4
DECLARE Shape IS ONE OF
  Circle HAS radius IS A NUMBER
  Rectangle HAS width IS A NUMBER, height IS A NUMBER
  Point

`the plot` MEANS Rectangle WITH width IS 20, height IS 30
`the pond` MEANS Circle 4
```

Use [CONSIDER](/l4/reference/control-flow/CONSIDER.md) to tell the values apart.

## Type synonyms

`DECLARE Name IS Type` gives another name to an existing type. The two names are the same type.

```l4
DECLARE Age IS A NUMBER

GIVEN age IS AN Age
GIVETH A BOOLEAN
`is an adult` age MEANS age >= 18
```

- A synonym can take type parameters: `DECLARE Optional OF a IS A MAYBE a`.
- A synonym cannot refer to itself, directly or through other synonyms; L4 reports a circular dependency between them.
- L4 follows a chain of synonyms for at most 1000 steps. For a longer chain, declare the first synonym as the type the chain ends in.

## Parameterised types

A type can take type parameters, so that the same declaration works for numbers, text or any other type.
Write the parameters after the name, with `OF` and commas, or declare them with `GIVEN … IS A TYPE` above the `DECLARE`:

```l4
DECLARE Box OF a HAS contents IS AN a

GIVEN a IS A TYPE
      b IS A TYPE
DECLARE `Pair of values` HAS first IS AN a, second IS A b

GIVETH A Box OF NUMBER
`a box of 42` MEANS Box WITH contents IS 42
```

The parameter can also follow the name without `OF` (`DECLARE Box a HAS …`).
A use of the type gives the parameters with `OF`: `Box OF NUMBER`, `` `Pair of values` OF NUMBER, STRING ``.

## A and AN

`A` and `AN` before a type are for readability only. They are interchangeable and can be left out: `IS A NUMBER`, `IS AN Account` and `IS NUMBER` mean the same.

```l4
DECLARE Account HAS balance IS A NUMBER

GIVEN account IS AN Account
GIVETH NUMBER
`balance of` account MEANS account's balance
```

## Type expressions

Wherever a type is expected (after `IS A`, `GIVETH`, in a field):

| Type expression                          | Meaning                                    |
| ---------------------------------------- | ------------------------------------------ |
| `NUMBER`, `STRING`, `BOOLEAN`, `DATE`    | a built-in type ([Types](/l4/reference/types.md))       |
| `LIST OF Type`                           | a list                                     |
| `MAYBE Type`                             | a value that may be missing                |
| `EITHER Type1 Type2`                     | one of two types                           |
| `Name OF Type1, Type2`                   | a parameterised type, given its parameters |
| `FUNCTION FROM Type1 AND Type2 TO Type3` | a function                                 |
| `TYPE`                                   | a type parameter                           |

## Related pages

- [Types](/l4/reference/types.md): built-in types
- [ASSUME](/l4/reference/types/ASSUME.md): a name with a type and no definition
- [FOR ALL](/l4/reference/types/for-all.md): polymorphic types for `ASSUME`
- [AKA](/l4/reference/functions/AKA.md): other names for a type
