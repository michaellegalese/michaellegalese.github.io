# MEANS

`MEANS` gives the body of a function or a value, written without `DECIDE`.

## Syntax

```text
name MEANS expression
name input1 input2 ... MEANS expression
```

`name MEANS expression` defines the same thing as `DECIDE name IS expression`.
The two keywords do not mix: `DECIDE name MEANS ...` and a top-level `name IS ...` are syntax errors.

**Example file:** 

```l4-file
-- MEANS keyword examples

-- Basic MEANS Usage

-- Simple value definition
greeting MEANS "Hello, World!"
answer MEANS 42

#EVAL greeting
#EVAL answer

-- Functions with MEANS

GIVEN x IS A NUMBER
double x MEANS x TIMES 2

GIVEN a IS A NUMBER, b IS A NUMBER
add a b MEANS a PLUS b

#EVAL double 21
#EVAL add 10 32

-- Multi-line Function Bodies

GIVEN n IS A NUMBER
GIVETH A NUMBER
factorial n MEANS
  IF n EQUALS 0
  THEN 1
  ELSE n TIMES factorial (n MINUS 1)

#EVAL factorial 6

-- MEANS with WHERE Clauses

GIVEN radius IS A NUMBER
circumference radius MEANS
  2 TIMES pi TIMES radius
  WHERE
    pi MEANS 3.14159

#EVAL circumference 10

-- Nested WHERE
outerFunction MEANS
  innerResult TIMES 2
  WHERE
    innerResult MEANS
      baseValue PLUS 10
      WHERE
        baseValue MEANS 5

#EVAL outerFunction

-- MEANS vs IS Comparison

-- Using MEANS for definitions
valueWithMeans MEANS 100
valueWithMeans2 MEANS 200

#EVAL valueWithMeans
#EVAL valueWithMeans2

-- MEANS in Complex Expressions

GIVEN items IS A LIST OF NUMBER
sumItems items MEANS
  CONSIDER items
  WHEN EMPTY THEN 0
  WHEN head FOLLOWED BY tail THEN head PLUS sumItems tail

#EVAL sumItems (LIST 1, 2, 3, 4, 5)
```



## Examples

```l4
`the greeting` MEANS "Hello, World!"

GIVEN n IS A NUMBER
GIVETH A NUMBER
`factorial of` n MEANS
  IF n EQUALS 0
  THEN 1
  ELSE n TIMES `factorial of` (n MINUS 1)
```

The inputs can sit before, between or after the words of the name, so a call reads like a sentence:

```l4
GIVEN buyer IS A STRING
      seller IS A STRING
GIVETH A STRING
buyer `buys from` seller MEANS CONCAT buyer, " buys from ", seller

#EVAL "Alice" `buys from` "Bob"
```

See [Mixfix functions](/l4/reference/functions.md#mixfix-functions).

## Other places MEANS appears

- Under `WHERE`, each local definition uses `MEANS`; see [LET and WHERE](/l4/reference/functions/LET.md#where).
- After `LET`, `MEANS` is one of the keywords that can introduce a definition, with `IS`, `BE`, `MEAN` and `=`.
- Inside a `DECLARE … HAS`, a field with `MEANS` is a computed field; see [DECLARE: Computed fields](/l4/reference/types/DECLARE.md#computed-fields).
- A function can be written as several `MEANS` lines, one per case; see [DECIDE: Several clauses](/l4/reference/functions/DECIDE.md#several-clauses).

## Related pages

- [DECIDE](/l4/reference/functions/DECIDE.md)
- [GIVEN and GIVETH](/l4/reference/functions/GIVEN.md)
