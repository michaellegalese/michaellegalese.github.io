# YIELD

`GIVEN … YIELD` makes a function without a name (a lambda). It is mostly used to pass a small function to another function, such as `all`, `any`, `map` or `filter` from the [prelude](/l4/reference/libraries/prelude.md).

## Syntax

```text
GIVEN name YIELD expression
GIVEN name1, name2 YIELD expression
GIVEN name IS A Type YIELD expression
GIVEN name1
      name2
YIELD expression
```

The types of the inputs can be left out when L4 can infer them.

**Example file:** 

```l4-file
-- YIELD keyword examples: Anonymous functions (lambdas)

IMPORT prelude


-- BASIC LAMBDA SYNTAX


-- Simple single-parameter lambda
DECIDE `add one` IS GIVEN x YIELD x + 1

-- Multiple parameters (comma-separated)
DECIDE `add together` IS GIVEN x, y YIELD x + y

-- Multiple parameters (layout-based)
DECIDE `multiply values` IS
  GIVEN a
        b
  YIELD a * b


-- TYPED LAMBDAS


-- Single typed parameter
DECIDE `double number` IS GIVEN n IS A NUMBER YIELD n * 2

-- Multiple typed parameters
DECIDE `format pair` IS
  GIVEN `person name` IS A STRING
        `person age` IS A NUMBER
  YIELD `person name`


-- LAMBDAS WITH HIGHER-ORDER FUNCTIONS


-- Using lambda with `all` (universal quantifier)
GIVEN numbers IS A LIST OF NUMBER
GIVETH A BOOLEAN
`all positive` MEANS all (GIVEN n YIELD n > 0) numbers

-- Using lambda with `any` (existential quantifier)
GIVEN numbers IS A LIST OF NUMBER
GIVETH A BOOLEAN
`has negative` MEANS any (GIVEN n YIELD n < 0) numbers

-- Using lambda with `map`
GIVEN numbers IS A LIST OF NUMBER
GIVETH A LIST OF NUMBER
`doubled list` MEANS map (GIVEN n YIELD n * 2) numbers

-- Using lambda with `filter`
GIVEN numbers IS A LIST OF NUMBER
GIVETH A LIST OF NUMBER
`only positive` MEANS filter (GIVEN n YIELD n > 0) numbers


-- COMPLEX LAMBDA EXAMPLES


-- Record field access in lambda
DECLARE Person HAS
  name IS A STRING
  age IS A NUMBER

GIVEN people IS A LIST OF Person
GIVETH A BOOLEAN
`all adults` MEANS all (GIVEN p YIELD p's age >= 18) people

-- Nested lambdas (currying)
GIVEN predicate IS A FUNCTION FROM NUMBER TO BOOLEAN
GIVETH A FUNCTION FROM NUMBER TO BOOLEAN
negate predicate MEANS GIVEN x YIELD NOT predicate x

-- Lambda returning a function
GIVEN a IS A TYPE
      b IS A TYPE
      c IS A TYPE
      f IS A FUNCTION FROM b TO c
      g IS A FUNCTION FROM a TO b
GIVETH A FUNCTION FROM a TO c
compose f g MEANS GIVEN x IS AN a YIELD f (g x)


-- EVALUATION TESTS


#EVAL `add one` 5
#EVAL `add together` 3 7
#EVAL `double number` 21
```



## Examples

```l4
IMPORT prelude

GIVEN amounts IS A LIST OF NUMBER
GIVETH A BOOLEAN
`all positive` amounts MEANS all (GIVEN n YIELD n > 0) amounts

GIVEN amounts IS A LIST OF NUMBER
GIVETH A LIST OF NUMBER
`doubled` amounts MEANS map (GIVEN n YIELD n * 2) amounts
```

A lambda can also be the body of a named definition. These two define the same function:

```l4
GIVEN n IS A NUMBER
GIVETH A NUMBER
`double` n MEANS n * 2

DECIDE `double as a lambda` IS GIVEN n YIELD n * 2
```

## Parentheses

When a lambda is passed to a function, put it in parentheses.
An argument that uses `'s` needs parentheses too:

```l4
IMPORT prelude

DECLARE Person HAS
  name IS A STRING
  age IS A NUMBER
DECLARE Household HAS
  members IS A LIST OF Person

GIVEN household IS A Household
GIVETH A BOOLEAN
`all adults` household MEANS all (GIVEN p YIELD p's age >= 18) (household's members)
```

Without the parentheses, `all (GIVEN p YIELD p's age >= 18) household's members` is a syntax error.

## Related pages

- [GIVEN and GIVETH](/l4/reference/functions/GIVEN.md): inputs of a named function
- [MEANS](/l4/reference/functions/MEANS.md): named definitions
- [prelude](/l4/reference/libraries/prelude.md): `all`, `any`, `map`, `filter` and other functions that take a function
