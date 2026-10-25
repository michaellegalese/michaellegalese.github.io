# GIVEN and GIVETH

`GIVEN` declares the inputs of a function, each with a name and a type.
`GIVETH` declares the type of its result.
Both go on the lines above the definition.

## Syntax

```text
GIVEN name IS A Type
GIVEN name1 IS A Type1, name2 IS A Type2
GIVEN name1 IS A Type1
      name2 IS A Type2
GIVETH A Type
```

A function has one `GIVEN`. Further inputs go on the same line, separated by commas, or on the lines below, lined up under the first name.
A second `GIVEN` line directly below the first is a syntax error.

**Example files:** 

```l4-file
-- GIVEN keyword examples

-- Single Parameter Functions

GIVEN x IS A NUMBER
`double the value` x MEANS x TIMES 2

GIVEN `person name` IS A STRING
`greet the person` `person name` MEANS CONCAT "Hello, ", `person name`

#EVAL `double the value` 21
#EVAL `greet the person` "World"

-- Multiple Parameters (Same Line)

GIVEN a IS A NUMBER, b IS A NUMBER
`add two numbers` a b MEANS a PLUS b

GIVEN x IS A NUMBER, y IS A NUMBER
`multiply two numbers` x y MEANS x TIMES y

#EVAL `add two numbers` 10 32
#EVAL `multiply two numbers` 6 7

-- Multiple Parameters (Continuation with Indentation)

GIVEN x1 IS A NUMBER
      y1 IS A NUMBER
      z1 IS A NUMBER
`sum of three numbers` x1 y1 z1 MEANS x1 PLUS y1 PLUS z1

#EVAL `sum of three numbers` 1 2 3

-- Parameters with Complex Types

DECLARE Point HAS x IS A NUMBER, y IS A NUMBER

GIVEN p IS A Point
`get x coordinate` p MEANS p's x

GIVEN p1 IS A Point
      p2 IS A Point
`distance squared between points` p1 p2 MEANS
  LET `delta x` IS p2's x MINUS p1's x
      `delta y` IS p2's y MINUS p1's y
  IN (`delta x` TIMES `delta x`) PLUS (`delta y` TIMES `delta y`)

DECIDE `the origin` IS Point WITH x IS 0, y IS 0
DECIDE `point one` IS Point WITH x IS 3, y IS 4

#EVAL `get x coordinate` `point one`
#EVAL `distance squared between points` `the origin` `point one`

-- Type Parameters

GIVEN a IS A TYPE, xs IS A LIST OF a
GIVETH A NUMBER
`length of list` xs MEANS
  CONSIDER xs
  WHEN EMPTY THEN 0
  WHEN hd FOLLOWED BY tail THEN 1 PLUS `length of list` tail

#EVAL `length of list` (LIST 1, 2, 3, 4, 5)
#EVAL `length of list` (LIST "a", "b", "c")

-- Combined with GIVETH

GIVEN n IS A NUMBER
GIVETH A BOOLEAN
`is positive number` n MEANS n > 0

GIVEN num IS A NUMBER
GIVETH A STRING
`sign of number` num MEANS
  IF num > 0 THEN "positive"
  ELSE IF num < 0 THEN "negative"
       ELSE "zero"

#EVAL `is positive number` 5
#EVAL `sign of number` (-3)
```

, 

```l4-file
-- GIVETH keyword examples

-- Basic Return Types

GIVEN n IS A NUMBER
GIVETH A NUMBER
`the square of` n MEANS n TIMES n

GIVEN a IS A NUMBER
      b IS A NUMBER
GIVETH A NUMBER
`the larger of` a b MEANS IF a > b THEN a ELSE b

#EVAL `the square of` 7
#EVAL `the larger of` 10 25

-- Boolean Return Types

GIVEN age IS A NUMBER
GIVETH A BOOLEAN
`is an adult` age MEANS age >= 18

GIVEN n IS A NUMBER
GIVETH A BOOLEAN
`is even` n MEANS (n MODULO 2) EQUALS 0

#EVAL `is an adult` 21
#EVAL `is even` 4

-- String Return Types

GIVEN name IS A STRING
GIVETH A STRING
`greeting for` name MEANS CONCAT "Hello, ", name

GIVEN num IS A NUMBER
GIVETH A STRING
`description of` num MEANS
  IF num > 0 THEN "positive"
  ELSE IF num < 0 THEN "negative"
       ELSE "zero"

#EVAL `greeting for` "Alice"
#EVAL `description of` (-5)

-- Complex Return Types

-- Returning MAYBE
GIVEN xs IS A LIST OF NUMBER
GIVETH A MAYBE NUMBER
`the first of` xs MEANS
  CONSIDER xs
  WHEN EMPTY THEN NOTHING

  WHEN h FOLLOWED BY rest THEN JUST h

#EVAL `the first of` (LIST 1, 2, 3)
#EVAL `the first of` EMPTY

-- Returning LIST
GIVEN n IS A NUMBER
GIVETH A LIST OF NUMBER
`countdown from` n MEANS
  IF n <= 0 THEN EMPTY
  ELSE n FOLLOWED BY `countdown from` (n MINUS 1)

#EVAL `countdown from` 5

-- GIVES as Synonym

GIVEN x IS A NUMBER
GIVES A NUMBER
`three times` x MEANS x TIMES 3

#EVAL `three times` 7

-- Parameterized Return Types

GIVEN a IS A TYPE, x IS AN a
GIVETH A LIST OF a
`list holding only` x MEANS x FOLLOWED BY EMPTY

#EVAL `list holding only` 42
#EVAL `list holding only` "hello"

-- Record Return Types

DECLARE Person HAS name IS A STRING, age IS A NUMBER

GIVEN `person name` IS A STRING
      `person age` IS A NUMBER
GIVETH A Person
`make a person` `person name` `person age` MEANS Person WITH name IS `person name`, age IS `person age`

#EVAL `make a person` "Bob" 30
```



## GIVEN

```l4
GIVEN amount IS A NUMBER
      `days overdue` IS A NUMBER
GIVETH A NUMBER
`late fee` amount `days overdue` MEANS
  IF `days overdue` > 30 THEN amount TIMES 0.05 ELSE 0
```

The names after the function name are the `GIVEN` inputs in order; they may be left out of the definition, but if they are written they must match.
`A` and `AN` are interchangeable (`IS AN Invoice`); see [DECLARE](/l4/reference/types/DECLARE.md#a-and-an).

### Calling with named inputs

A call gives the inputs in the order of the `GIVEN`, or by name with `WITH`:

```l4
GIVEN amount IS A NUMBER
      `days overdue` IS A NUMBER
GIVETH A NUMBER
`late fee` amount `days overdue` MEANS
  IF `days overdue` > 30 THEN amount TIMES 0.05 ELSE 0

#EVAL `late fee` 1000 45
#EVAL `late fee` WITH amount IS 1000, `days overdue` IS 45
#ASSERT (`late fee` WITH `days overdue` IS 10, amount IS 1000) EQUALS 0
```

With `WITH`, the inputs can be given in any order. Put the call in parentheses when an operator follows it, as in the `#ASSERT`. This works only for a function with a `GIVEN`; a value declared with [ASSUME](/l4/reference/types/ASSUME.md) cannot be given a value this way.

### Type parameters

An input of type `TYPE` makes the function work for any type:

```l4
GIVEN a IS A TYPE
      xs IS A LIST OF a
GIVETH A NUMBER
`number of items in` xs MEANS
  CONSIDER xs
  WHEN EMPTY THEN 0
  WHEN first FOLLOWED BY rest THEN 1 PLUS `number of items in` rest
```

A type parameter is not passed when the function is called: `` `number of items in` (LIST 1, 2, 3) `` gives `3`.
[FOR ALL](/l4/reference/types/for-all.md) writes the same kind of type for an `ASSUME`.

### Descriptions of inputs

`@desc` after an input describes it. The description is shown when the function is deployed with `@export`:

```l4
@export Calculate the late fee for an overdue invoice
GIVEN amount IS A NUMBER @desc The invoice amount
      `days overdue` IS A NUMBER @desc How many days past the due date
GIVETH A NUMBER
`late fee` amount `days overdue` MEANS
  IF `days overdue` > 30 THEN amount TIMES 0.05 ELSE 0
```

### Anonymous functions

`GIVEN … YIELD` makes a function without a name; see [YIELD](/l4/reference/functions/YIELD.md).

## GIVETH

`GIVETH` names the type of the result. `GIVES` is a synonym, and the article is optional (`GIVETH NUMBER`).

```l4
GIVEN xs IS A LIST OF NUMBER
GIVETH A MAYBE NUMBER
`the first of` xs MEANS
  CONSIDER xs
  WHEN EMPTY THEN NOTHING
  WHEN first FOLLOWED BY rest THEN JUST first
```

`GIVETH` is optional: without it, L4 infers the result type.
Writing it makes the rule easier to read and lets the type checker report a body that gives the wrong type.

A rule that returns obligations has the result type `DEONTIC Party Action`; see [DEONTIC](/l4/reference/regulative/DEONTIC.md).

## Related pages

- [DECIDE](/l4/reference/functions/DECIDE.md): the definition that follows `GIVEN` and `GIVETH`
- [Types](/l4/reference/types.md): the types an input or result can have
- [ASSUME](/l4/reference/types/ASSUME.md): a name with a type and no definition
