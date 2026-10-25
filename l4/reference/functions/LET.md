# LET and WHERE

`LET` and `WHERE` give names to values that are used only inside one expression.
`LET` puts the definitions before the expression; `WHERE` puts them after it.
Use whichever matches the order of the legal text.

## Syntax

```text
LET name IS expression IN body

LET
  name1 IS expression1
  name2 IS expression2
IN body

body
WHERE
  name1 MEANS expression1
  name2 MEANS expression2
```

Several definitions go one per line, lined up in the same column; commas between them are a syntax error.

**Example files:** 

```l4-file
-- LET keyword examples

-- Single Binding

DECIDE `result with single binding` IS
  LET x IS 5
  IN x TIMES 2

#EVAL `result with single binding`

-- Multiple Bindings (Inline)

DECIDE `sum of two values` IS
  LET
    x IS 5
    y IS 10
  IN x PLUS y

#EVAL `sum of two values`

-- Multiple Bindings (Multi-line)

DECIDE `sum of three values` IS
  LET
    x IS 5
    y IS 10
    z IS 15
  IN x PLUS y PLUS z

#EVAL `sum of three values`

-- Forward References

DECIDE `calculated result using forward references` IS
  LET
    `initial value` IS 5
    `doubled value` IS `initial value` TIMES 2
    `final value` IS `doubled value` PLUS 3
  IN `final value`

#EVAL `calculated result using forward references`

-- Various Binding Keywords

DECIDE `sum using various binding keywords` IS
  LET
    a IS 1
    b BE 2
    c MEAN 3
    d MEANS 4
    e = 5
  IN a PLUS b PLUS c PLUS d PLUS e

#EVAL `sum using various binding keywords`

-- Using = Operator

DECIDE `using equals for binding` IS
  LET
    val = 42
    result = val PLUS 8
  IN result

#EVAL `using equals for binding`

-- Nested LET Expressions

DECIDE `nested let expressions` IS
  LET `step one` IS 10
  IN LET `step two` IS `step one` TIMES 2
     IN LET `step three` IS `step two` PLUS 5
        IN `step three`

#EVAL `nested let expressions`

-- LET with Complex Expressions

GIVEN n IS A NUMBER
`factorial of` n MEANS
  IF n <= 1
  THEN 1
  ELSE LET `previous factorial` IS `factorial of` (n MINUS 1)
       IN n TIMES `previous factorial`

#EVAL `factorial of` 5

-- LET with Records

DECLARE Point HAS x IS A NUMBER, y IS A NUMBER

DECIDE `shifted point example` IS
  LET
    `the origin` IS Point WITH x IS 0, y IS 0
    `shifted point` IS Point WITH x IS `the origin`'s x PLUS 10, y IS `the origin`'s y PLUS 20
  IN `shifted point`

#EVAL `shifted point example`

-- LET with Lists

DECIDE `list of numbers example` IS
  LET
    `all numbers` IS LIST 1, 2, 3, 4, 5
  IN `all numbers`

#EVAL `list of numbers example`

-- LET in Function Definitions

GIVEN a IS A NUMBER
      b IS A NUMBER
      c IS A NUMBER
`quadratic discriminant` a b c MEANS
  LET
    `the discriminant` IS b TIMES b MINUS 4 TIMES a TIMES c
  IN `the discriminant`

#EVAL `quadratic discriminant` 1 5 6
```

, 

```l4-file
-- WHERE keyword examples

-- Basic Local Definitions

GIVEN radius IS A NUMBER
`area of circle` radius MEANS
  pi TIMES radius TIMES radius
  WHERE
    pi MEANS 3.14159

#EVAL `area of circle` 10

-- Multiple Local Definitions

GIVEN x IS A NUMBER, y IS A NUMBER
`sum times difference` x y MEANS
  `sum value` TIMES `diff value`
  WHERE
    `sum value` MEANS x PLUS y
    `diff value` MEANS x MINUS y

#EVAL `sum times difference` 10 3

-- Avoiding Repetition

GIVEN a IS A NUMBER
      b IS A NUMBER
      c IS A NUMBER
`quadratic discriminant` a b c MEANS
  `b squared` MINUS `four a c`
  WHERE
    `b squared` MEANS b TIMES b
    `four a c` MEANS 4 TIMES a TIMES c

#EVAL `quadratic discriminant` 1 5 6

-- Nested WHERE Clauses

outer MEANS
  middle
  WHERE
    middle MEANS
      inner
      WHERE
        inner MEANS 42

#EVAL outer

-- Local Functions

GIVEN n1 IS A NUMBER, n2 IS A NUMBER
`sum of squares` n1 n2 MEANS
  `square of` n1 PLUS `square of` n2
  WHERE
    `square of` n MEANS n TIMES n

#EVAL `sum of squares` 3 4

-- Complex Example: Pythagorean Distance

GIVEN x1 IS A NUMBER
      y1 IS A NUMBER
      x2 IS A NUMBER
      y2 IS A NUMBER
`distance between points` x1 y1 x2 y2 MEANS
  `square root` (`dx squared` PLUS `dy squared`)
  WHERE
    `delta x` MEANS x2 MINUS x1
    `delta y` MEANS y2 MINUS y1
    `dx squared` MEANS `delta x` TIMES `delta x`
    `dy squared` MEANS `delta y` TIMES `delta y`
    `square root` n MEANS sqrt n

IMPORT math

#EVAL `distance between points` 0 0 3 4

-- WHERE with Pattern Matching

GIVEN xs IS A LIST OF NUMBER
GIVETH A NUMBER
`sum of list using where` xs MEANS
  CONSIDER xs
  WHEN EMPTY THEN 0
  WHEN head FOLLOWED BY tail THEN head PLUS `rest sum`
    WHERE
      `rest sum` MEANS `sum of list using where` tail

#EVAL `sum of list using where` (LIST 1, 2, 3, 4, 5)
```



## LET

```l4
GIVEN price IS A NUMBER
GIVETH A NUMBER
`total price` price MEANS
  LET
    `the tax` IS price TIMES 0.2
    `the fee` IS 5
  IN price PLUS `the tax` PLUS `the fee`
```

After `LET`, a definition can use `IS`, `BE`, `MEAN`, `MEANS` or `=`:

```l4
DECIDE `the sum` IS
  LET
    a IS 1
    b BE 2
    c MEAN 3
    d MEANS 4
    e = 5
  IN a PLUS b PLUS c PLUS d PLUS e
```

## WHERE

```l4
GIVEN radius IS A NUMBER
GIVETH A NUMBER
`area of a circle` radius MEANS
  `pi` TIMES radius TIMES radius
  WHERE
    `pi` MEANS 3.14159
```

A definition under `WHERE` can take inputs, so it can be a local function, with or without its own `GIVEN` and `GIVETH`:

```l4
GIVEN a IS A NUMBER
      b IS A NUMBER
GIVETH A NUMBER
`sum of squares` a b MEANS
  `square of` a PLUS `square of` b
  WHERE
    GIVEN n IS A NUMBER
    GIVETH A NUMBER
    `square of` n MEANS n TIMES n
```

A `WHERE` can be nested inside a definition under another `WHERE`.

## Scope

- The names are visible only in the expression they belong to: after `IN` for `LET`, and in the body above for `WHERE`.
- Definitions in one `LET` or one `WHERE` can refer to each other, in any order.
- They can use the function's `GIVEN` inputs and any name defined outside.
- A rule written as [several clauses](/l4/reference/functions/DECIDE.md#several-clauses) cannot be defined inside `LET` or `WHERE`.

## Related pages

- [DECIDE](/l4/reference/functions/DECIDE.md) and [MEANS](/l4/reference/functions/MEANS.md): top-level definitions
- [Error messages: I could not find a definition for the identifier](/l4/reference/errors.md#i-could-not-find-a-definition-for-the-identifier)
