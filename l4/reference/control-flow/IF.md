# IF

`IF … THEN … ELSE` chooses between two values by a BOOLEAN condition.
It is an expression: it always gives a value, so the `ELSE` is required.

## Syntax

```text
IF condition THEN value-if-true ELSE value-if-false

IF condition
THEN value-if-true
ELSE value-if-false
```

Both branches must have the same type ([Branch type mismatch](/l4/reference/errors.md#branch-type-mismatch)). An `IF` without `ELSE` is a syntax error ([IF without ELSE](/l4/reference/errors.md#if-without-else)).

**Example file:** 

```l4-file
-- IF keyword examples

-- Basic If-Then-Else

-- Simple conditional
DECIDE `basic if example` IS IF TRUE THEN "yes" ELSE "no"

-- With variable
GIVEN x IS A NUMBER
`check if positive` x MEANS IF x > 0 THEN "positive" ELSE "not positive"

#EVAL `basic if example`
#EVAL `check if positive` 5
#EVAL `check if positive` -3

-- Multi-line Format

GIVEN n IS A NUMBER
`absolute value of` n MEANS
  IF n >= 0
  THEN n
  ELSE 0 MINUS n

#EVAL `absolute value of` 5
#EVAL `absolute value of` -5

-- Nested If-Else (Chained Conditions)

GIVEN score IS A NUMBER
`letter grade for` score MEANS
  IF score >= 90 THEN "A"
  ELSE IF score >= 80 THEN "B"
       ELSE IF score >= 70 THEN "C"
            ELSE IF score >= 60 THEN "D"
                 ELSE "F"

#EVAL `letter grade for` 95
#EVAL `letter grade for` 75
#EVAL `letter grade for` 50

-- Complex Conditions

GIVEN age IS A NUMBER
      `has valid ID` IS A BOOLEAN
      `is member` IS A BOOLEAN
`can enter venue` age `has valid ID` `is member` MEANS
  IF (age >= 21 AND `has valid ID`) OR `is member`
  THEN "Welcome"
  ELSE "Access denied"

#EVAL `can enter venue` 25 TRUE FALSE
#EVAL `can enter venue` 18 TRUE FALSE
#EVAL `can enter venue` 18 FALSE TRUE

-- DECIDE ... IF Form (Boolean Functions)

-- Shorthand for boolean-returning functions
GIVEN `person age` IS A NUMBER
DECIDE `is adult` IF `person age` >= 18

-- Equivalent long form:
GIVEN `person age 2` IS A NUMBER
DECIDE `is adult 2` IS IF `person age 2` >= 18 THEN TRUE ELSE FALSE

-- Both work the same way; an input can also be given by name with WITH
#EVAL `is adult` 21
#EVAL `is adult 2` WITH `person age 2` IS 15

-- Returning Different Types

-- Returning numbers
GIVEN a IS A NUMBER, b IS A NUMBER
`maximum number` a b MEANS IF a > b THEN a ELSE b

-- Returning lists
GIVEN condition IS A BOOLEAN
`list if true otherwise empty` condition MEANS
  IF condition
  THEN LIST 1, 2, 3
  ELSE EMPTY

#EVAL `maximum number` 10 25
#EVAL `list if true otherwise empty` TRUE
#EVAL `list if true otherwise empty` FALSE

-- If in Complex Expressions

GIVEN n IS A NUMBER
GIVETH A NUMBER
`fibonacci number` n MEANS
  IF n <= 1
  THEN n
  ELSE `fibonacci number` (n MINUS 1) PLUS `fibonacci number` (n MINUS 2)

#EVAL `fibonacci number` 10
```



## Examples

```l4
GIVEN age IS A NUMBER
GIVETH A STRING
`age group of` age MEANS IF age >= 18 THEN "adult" ELSE "minor"

GIVEN x IS A NUMBER
GIVETH A NUMBER
`absolute value of` x MEANS
  IF x >= 0
  THEN x
  ELSE 0 MINUS x
```

The condition can be any BOOLEAN expression, built with [AND, OR, NOT](/l4/reference/operators/logical.md):

```l4
GIVEN age IS A NUMBER
      `has a licence` IS A BOOLEAN
GIVETH A STRING
`driving status` age `has a licence` MEANS
  IF age >= 16 AND `has a licence`
  THEN "may drive"
  ELSE "may not drive"
```

## Several conditions

An `IF` can go inside the `ELSE` of another. Each inner `IF` must be indented further than the one it belongs to:

```l4
GIVEN score IS A NUMBER
GIVETH A STRING
`grade for` score MEANS
  IF score >= 90 THEN "A"
  ELSE IF score >= 80 THEN "B"
       ELSE IF score >= 70 THEN "C"
            ELSE "F"
```

Lining up every `ELSE IF` in one column is an indentation error from the second one on. For more than two conditions, [BRANCH](/l4/reference/control-flow/BRANCH.md) is flatter.

A rule that is only a condition, such as "a person is eligible if …", is better written `DECIDE … IF`; see [DECIDE IF](/l4/reference/functions/DECIDE.md#decide-if).

## Related pages

- [BRANCH](/l4/reference/control-flow/BRANCH.md): several conditions, tried in order
- [CONSIDER](/l4/reference/control-flow/CONSIDER.md): choose by the shape of a value
- [Control flow](/l4/reference/control-flow.md)
