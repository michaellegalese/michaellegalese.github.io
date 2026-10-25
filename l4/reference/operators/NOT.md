> This page has moved to [/l4/reference/operators/logical](/l4/reference/operators/logical.md#not).

# AND, OR, NOT, IMPLIES

The logical operators combine BOOLEAN values.

## Forms

| Operator  | Symbol | Asyndetic | Meaning                                |
| --------- | ------ | --------- | -------------------------------------- |
| `AND`     | `&&`   | `...`     | both are TRUE                          |
| `OR`      | `\|\|` | `..`      | at least one is TRUE                   |
| `NOT`     |        |           | the opposite                           |
| `IMPLIES` | `=>`   |           | if the first is TRUE, so is the second |

The word and the symbol mean the same and can be mixed. For `...` and `..`, see [Operators: Asyndetic AND and OR](/l4/reference/operators.md#asyndetic-and-and-or).
For exceptions ("… unless …"), see [UNLESS](/l4/reference/operators/UNLESS.md).

**Example files:** 

```l4-file
-- AND keyword examples

-- Basic AND Operations

#EVAL TRUE AND TRUE
#EVAL TRUE AND FALSE
#EVAL FALSE AND TRUE
#EVAL FALSE AND FALSE

-- Symbolic Alternative (&&)

#EVAL TRUE && TRUE
#EVAL TRUE && FALSE

-- In Conditional Logic

ASSUME age IS A NUMBER
ASSUME `has license` IS A BOOLEAN

DECIDE `can drive` IS age >= 16 AND `has license`

#CHECK `can drive`

-- Chaining Multiple Conditions

GIVEN a IS A BOOLEAN
      b IS A BOOLEAN
      c IS A BOOLEAN
`all true` a b c MEANS a AND b AND c

#EVAL `all true` TRUE TRUE TRUE
#EVAL `all true` TRUE TRUE FALSE

-- Layout-Sensitive AND (Vertical Format)

ASSUME `person age` IS A NUMBER
ASSUME `person income` IS A NUMBER
ASSUME `has criminal record` IS A BOOLEAN

DECIDE `is eligible` IS
      `person age` >= 18
  AND `person income` > 30000
  AND NOT `has criminal record`

#CHECK `is eligible`

-- Complex Expressions with AND

GIVEN x IS A NUMBER, y IS A NUMBER
`in range` x y MEANS x >= 0 AND x <= 100 AND y >= 0 AND y <= 100

#EVAL `in range` 50 50
#EVAL `in range` 150 50

-- AND with Comparisons

GIVEN score IS A NUMBER
`passing grade` score MEANS score >= 60 AND score <= 100

#EVAL `passing grade` 75
#EVAL `passing grade` 55

-- AND in IF Expressions

GIVEN `user age` IS A NUMBER, `has permission` IS A BOOLEAN
`access level` `user age` `has permission` MEANS
  IF `user age` >= 18 AND `has permission`
  THEN "full access"
  ELSE "restricted"

#EVAL `access level` 25 TRUE
#EVAL `access level` 25 FALSE
```

, 

```l4-file
-- OR keyword examples

-- Basic OR Operations

#EVAL TRUE OR TRUE
#EVAL TRUE OR FALSE
#EVAL FALSE OR TRUE
#EVAL FALSE OR FALSE

-- Symbolic Alternative (||)

#EVAL TRUE || TRUE
#EVAL TRUE || FALSE

-- In Conditional Logic

ASSUME `is student` IS A BOOLEAN
ASSUME `is senior` IS A BOOLEAN

DECIDE `gets discount` IS `is student` OR `is senior`

#CHECK `gets discount`

-- Chaining Multiple Conditions

GIVEN a IS A BOOLEAN
      b IS A BOOLEAN
      c IS A BOOLEAN
`any true` a b c MEANS a OR b OR c

#EVAL `any true` FALSE FALSE FALSE
#EVAL `any true` FALSE TRUE FALSE

-- Layout-Sensitive OR (Vertical Format)

ASSUME `is admin` IS A BOOLEAN
ASSUME `is owner` IS A BOOLEAN
ASSUME `has permission` IS A BOOLEAN

DECIDE `has access` IS
      `is admin`
  OR  `is owner`
  OR  `has permission`

#CHECK `has access`

-- Complex Expressions with OR

GIVEN status IS A STRING
`is terminal status` status MEANS
      status EQUALS "completed"
  OR  status EQUALS "cancelled"
  OR  status EQUALS "failed"

#EVAL `is terminal status` "completed"
#EVAL `is terminal status` "pending"

-- OR in IF Expressions

GIVEN `user type` IS A STRING, `has VIP` IS A BOOLEAN
`get priority` `user type` `has VIP` MEANS
  IF `user type` EQUALS "admin" OR `has VIP`
  THEN "high"
  ELSE "normal"

#EVAL `get priority` "user" TRUE
#EVAL `get priority` "user" FALSE

-- Combining AND and OR

GIVEN x IS A NUMBER, y IS A BOOLEAN
`complex condition` x y MEANS
  (x > 0 AND x < 100) OR y

#EVAL `complex condition` 50 FALSE
#EVAL `complex condition` 150 FALSE
#EVAL `complex condition` 150 TRUE
```

, 

```l4-file
-- NOT examples

-- Basic NOT

#EVAL NOT TRUE
#EVAL NOT FALSE
#EVAL NOT NOT TRUE

-- With AND, on separate lines: NOT applies to its own line only

GIVEN age IS A NUMBER
      `is blocked` IS A BOOLEAN
GIVETH A BOOLEAN
`may access` age `is blocked` MEANS
      age >= 18
  AND NOT `is blocked`

#EVAL `may access` 30 FALSE
#EVAL `may access` 30 TRUE

-- On one line, NOT applies to everything after it

GIVEN a IS A BOOLEAN
      b IS A BOOLEAN
GIVETH A BOOLEAN
`not both` a b MEANS NOT a AND b
-- means NOT (a AND b)

GIVETH A BOOLEAN
`not a, and b` a b MEANS (NOT a) AND b
-- parentheses limit NOT to a

#EVAL `not both` TRUE FALSE
#EVAL `not a, and b` TRUE FALSE

-- With comparisons: no parentheses needed

GIVEN n IS A NUMBER
GIVETH A BOOLEAN
`is not zero` n MEANS NOT n EQUALS 0

#EVAL `is not zero` 5
#EVAL `is not zero` 0

-- In IF expressions

GIVEN flag IS A BOOLEAN
GIVETH A STRING
`flag message` flag MEANS
  IF NOT flag
  THEN "flag is off"
  ELSE "flag is on"

#EVAL `flag message` TRUE
#EVAL `flag message` FALSE
```

, 

```l4-file
-- IMPLIES keyword examples

-- Basic IMPLIES Operations

#EVAL TRUE IMPLIES TRUE
#EVAL TRUE IMPLIES FALSE
#EVAL FALSE IMPLIES TRUE
#EVAL FALSE IMPLIES FALSE

-- Symbolic Alternative (=>)

#EVAL TRUE => TRUE
#EVAL TRUE => FALSE
#EVAL FALSE => TRUE

-- Equivalence to NOT A OR B

GIVEN a IS A BOOLEAN
      b IS A BOOLEAN

-- These are logically equivalent
`implies version` a b MEANS a IMPLIES b
`or version` a b MEANS NOT a OR b

-- Test equivalence
#EVAL `implies version` TRUE TRUE
#EVAL `or version` TRUE TRUE

#EVAL `implies version` TRUE FALSE
#EVAL `or version` TRUE FALSE

#EVAL `implies version` FALSE TRUE
#EVAL `or version` FALSE TRUE

-- Legal Rule Examples

-- "If you are an employee, you must have a badge"
ASSUME `is employee` IS A BOOLEAN
ASSUME `has badge` IS A BOOLEAN

DECIDE `badge rule` IS `is employee` IMPLIES `has badge`

#CHECK `badge rule`

-- "A minor must have parental consent"
ASSUME `person age` IS A NUMBER
ASSUME `has parental consent` IS A BOOLEAN

DECIDE `consent rule` IS (`person age` < 18) IMPLIES `has parental consent`

#CHECK `consent rule`

-- Chained Implications

-- Implication is right-associative: A => B => C means A => (B => C)
GIVEN x IS A BOOLEAN
      y IS A BOOLEAN
      z IS A BOOLEAN
`chained impl` x y z MEANS x => y => z

#EVAL `chained impl` TRUE TRUE TRUE
#EVAL `chained impl` TRUE TRUE FALSE
#EVAL `chained impl` FALSE FALSE FALSE

-- Combined with AND/OR

GIVEN p IS A BOOLEAN
      q IS A BOOLEAN
      r IS A BOOLEAN

-- "(P AND Q) implies R"
`conjunction implies` p q r MEANS (p AND q) IMPLIES r

-- "P implies (Q OR R)"
`implies disjunction` p q r MEANS p IMPLIES (q OR r)

#EVAL `conjunction implies` TRUE TRUE TRUE
#EVAL `conjunction implies` TRUE TRUE FALSE
#EVAL `implies disjunction` TRUE FALSE TRUE
#EVAL `implies disjunction` TRUE FALSE FALSE
```



## Truth table

| A     | B     | A AND B | A OR B | NOT A | A IMPLIES B |
| ----- | ----- | ------- | ------ | ----- | ----------- |
| TRUE  | TRUE  | TRUE    | TRUE   | FALSE | TRUE        |
| TRUE  | FALSE | FALSE   | TRUE   | FALSE | FALSE       |
| FALSE | TRUE  | FALSE   | TRUE   | TRUE  | TRUE        |
| FALSE | FALSE | FALSE   | FALSE  | TRUE  | TRUE        |

## AND

```l4
GIVEN age IS A NUMBER
      `has a licence` IS A BOOLEAN
GIVETH A BOOLEAN
`may drive` age `has a licence` MEANS age >= 16 AND `has a licence`
```

## OR

```l4
GIVEN `is an employee` IS A BOOLEAN
      `is a contractor` IS A BOOLEAN
GIVETH A BOOLEAN
`may enter` `is an employee` `is a contractor` MEANS `is an employee` OR `is a contractor`
```

## NOT

`NOT` negates everything after it on its line, and any lines below that are indented further than the `NOT`:

| Written             | Means                 |
| ------------------- | --------------------- |
| `NOT a AND b`       | `NOT (a AND b)`       |
| `a AND NOT b AND c` | `a AND NOT (b AND c)` |
| `NOT n EQUALS 0`    | `NOT (n EQUALS 0)`    |

To negate only one operand on a line, put the `NOT` and its operand in parentheses: `(NOT a) AND b`.
In the vertical layout below, each line is a separate operand, so `NOT` applies to its own line only.

## IMPLIES

`A IMPLIES B` is FALSE only when `A` is TRUE and `B` is FALSE. It means the same as `NOT A OR B`: "if A, then B".

```l4
GIVEN age IS A NUMBER
      `has parental consent` IS A BOOLEAN
GIVETH A BOOLEAN
`consent rule is met` age `has parental consent` MEANS
  age < 18 IMPLIES `has parental consent`
```

`IMPLIES` groups to the right: `a IMPLIES b IMPLIES c` means `a IMPLIES (b IMPLIES c)`.

## Several lines

A long condition can be written one operand per line, with the operator at the start of each line:

```l4
GIVEN age IS A NUMBER
      income IS A NUMBER
      `has a criminal record` IS A BOOLEAN
GIVETH A BOOLEAN
`is eligible` age income `has a criminal record` MEANS
      age >= 18
  AND income > 30000
  AND NOT `has a criminal record`
```

When the operators are in one column, the usual [precedence](/l4/reference/operators.md#precedence) applies.
An operator written further left than the lines above it applies to all of them together:

```l4
GIVEN a IS A BOOLEAN
      b IS A BOOLEAN
      c IS A BOOLEAN
GIVETH A BOOLEAN
`grouped by layout` a b c MEANS
         a
      OR b
  AND    c
```

This means `(a OR b) AND c`; on one line, `a OR b AND c` means `a OR (b AND c)`.

## Evaluation order

The left operand is evaluated first, and the right one only when it is needed: `FALSE AND x` is FALSE, `TRUE OR x` is TRUE and `FALSE IMPLIES x` is TRUE without looking at `x`.

## Related pages

- [UNLESS](/l4/reference/operators/UNLESS.md): exceptions
- [Operators](/l4/reference/operators.md): precedence and asyndetic forms
- [IF](/l4/reference/control-flow/IF.md): use a condition to choose a value
