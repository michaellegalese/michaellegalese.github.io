# FOR ALL

`FOR ALL` introduces type variables in a type, so that an `ASSUME` can declare a value or function that works for any type.

## Syntax

```text
FOR ALL a
FOR ALL a AND b
FOR ALL a AND b AND c
```

`FOR ALL` comes first in the type, followed by the type that uses the variables.

**Example file:** 

```l4-file
-- FOR ALL Examples
-- Universal quantifier for polymorphic type signatures

--------------------------------------------------------------------------------
-- Basic Polymorphic Type Declarations
--------------------------------------------------------------------------------

-- Identity function: works for any type
ASSUME identity IS
  FOR ALL a
  A FUNCTION FROM a TO a

-- Constant function: ignores second argument
ASSUME const IS
  FOR ALL a AND b
  A FUNCTION FROM a AND b TO a

--------------------------------------------------------------------------------
-- List Operations (Polymorphic)
--------------------------------------------------------------------------------

-- Map: apply function to each element
ASSUME map IS
  FOR ALL a AND b
  A FUNCTION
    FROM      FUNCTION FROM a TO b
          AND LIST OF a
    TO    LIST OF b

-- Filter: keep elements matching predicate
ASSUME filter IS
  FOR ALL a
  A FUNCTION
    FROM      FUNCTION FROM a TO BOOLEAN
          AND LIST OF a
    TO    LIST OF a

-- Fold: reduce list to single value
ASSUME foldl IS
  FOR ALL a AND b
  A FUNCTION
    FROM      FUNCTION FROM b AND a TO b
          AND b
          AND LIST OF a
    TO    b

--------------------------------------------------------------------------------
-- Custom Algebraic Types with Type Parameters
--------------------------------------------------------------------------------

-- A choice between two types (like Either in Haskell)
DECLARE Choice OF a, b
  IS ONE OF
    Left  HAS payload IS AN a
    Right HAS payload IS A  b

-- Optional value (like Maybe in Haskell)
DECLARE Optional OF a
  IS ONE OF
    None
    Some HAS value IS AN a

-- A pair of two values
DECLARE Pair OF a, b
  HAS first IS AN a
      second IS A b

--------------------------------------------------------------------------------
-- Functions Using Custom Types
--------------------------------------------------------------------------------

-- Handle either alternative of a Choice
ASSUME either IS
  FOR ALL a AND b AND c
  A FUNCTION
    FROM      FUNCTION FROM a TO c
          AND FUNCTION FROM b TO c
          AND Choice OF a, b
    TO    c

-- Extract value from Optional with default
ASSUME fromOptional IS
  FOR ALL a
  A FUNCTION
    FROM      a
          AND Optional OF a
    TO    a

-- Create a pair
ASSUME makePair IS
  FOR ALL a AND b
  A FUNCTION FROM a AND b TO Pair OF a, b

--------------------------------------------------------------------------------
-- Implementing Polymorphic Functions with GIVEN
--------------------------------------------------------------------------------

-- The definition style uses GIVEN ... IS A TYPE
GIVEN a IS A TYPE
      b IS A TYPE
      f IS A FUNCTION FROM a TO b
      list IS A LIST OF a
GIVETH A LIST OF b
myMap f list MEANS
  CONSIDER list
  WHEN EMPTY THEN EMPTY
  WHEN x FOLLOWED BY xs THEN f x FOLLOWED BY myMap f xs

-- Length works for any list type
GIVEN a IS A TYPE
      list IS A LIST OF a
GIVETH A NUMBER
myLength list MEANS
  CONSIDER list
  WHEN EMPTY THEN 0
  WHEN x FOLLOWED BY xs THEN 1 PLUS myLength xs

-- Reverse a list (simplified - just demonstrates the pattern)
GIVEN a IS A TYPE
      list IS A LIST OF a
GIVETH A LIST OF a
myReverse list MEANS
  CONSIDER list
  WHEN EMPTY THEN EMPTY
  WHEN x FOLLOWED BY xs THEN append (myReverse xs) (x FOLLOWED BY EMPTY)

-- Helper for append
GIVEN a IS A TYPE
      list1 IS A LIST OF a
      list2 IS A LIST OF a
GIVETH A LIST OF a
append list1 list2 MEANS
  CONSIDER list1
  WHEN EMPTY THEN list2
  WHEN x FOLLOWED BY xs THEN x FOLLOWED BY append xs list2

--------------------------------------------------------------------------------
-- Using Polymorphic Functions
--------------------------------------------------------------------------------

-- Test data
DECIDE numbers IS LIST 1, 2, 3, 4, 5
DECIDE strings IS LIST "a", "b", "c"

-- Apply myLength to different list types
#EVAL myLength numbers  -- 5
#EVAL myLength strings  -- 3

-- Map with different types
GIVEN n IS A NUMBER
double n MEANS n TIMES 2

#EVAL myMap double numbers  -- LIST 2, 4, 6, 8, 10

--------------------------------------------------------------------------------
-- Higher-Order Polymorphic Functions
--------------------------------------------------------------------------------

-- Function composition
ASSUME compose IS
  FOR ALL a AND b AND c
  A FUNCTION
    FROM      FUNCTION FROM b TO c
          AND FUNCTION FROM a TO b
    TO    FUNCTION FROM a TO c

-- Flip argument order
ASSUME flip IS
  FOR ALL a AND b AND c
  A FUNCTION
    FROM      FUNCTION FROM a AND b TO c
    TO    FUNCTION FROM b AND a TO c

--------------------------------------------------------------------------------
-- Polymorphic Tree Type
--------------------------------------------------------------------------------

DECLARE Tree OF a
  IS ONE OF
    Leaf HAS value IS AN a
    Node HAS
      left IS A Tree OF a
      right IS A Tree OF a

-- Count nodes in a tree
GIVEN a IS A TYPE
      tree IS A Tree OF a
GIVETH A NUMBER
treeSize tree MEANS
  CONSIDER tree
  WHEN Leaf v THEN 1
  WHEN Node l r THEN treeSize l PLUS treeSize r

-- Example tree
DECIDE exampleTree IS
  Node WITH
    left IS Leaf WITH value IS 1
    right IS Node WITH
      left IS Leaf WITH value IS 2
      right IS Leaf WITH value IS 3

#EVAL treeSize exampleTree  -- 3
```



## Examples

### One type variable

```l4
ASSUME identity IS
  FOR ALL a
  A FUNCTION FROM a TO a
```

`identity` takes a value of any type and returns a value of the same type.

### Several type variables

```l4
ASSUME map IS
  FOR ALL a AND b
  A FUNCTION
    FROM      FUNCTION FROM a TO b
          AND LIST OF a
    TO    LIST OF b
```

For all types `a` and `b`, `map` takes a function from `a` to `b` and a list of `a`, and returns a list of `b`.

### With a parameterised type

```l4
DECLARE Choice OF a, b
  IS ONE OF
    Left  HAS payload IS AN a
    Right HAS payload IS A  b

ASSUME choose IS
  FOR ALL a AND b AND c
  A FUNCTION
    FROM      FUNCTION FROM a TO c
          AND FUNCTION FROM b TO c
          AND Choice OF a, b
    TO    c
```

## FOR ALL and GIVEN

`FOR ALL` declares a polymorphic type with no definition, in an `ASSUME`.
A function that is defined gets its type variables from `GIVEN … IS A TYPE` instead:

```l4
GIVEN a IS A TYPE
      b IS A TYPE
      f IS A FUNCTION FROM a TO b
      list IS A LIST OF a
GIVETH A LIST OF b
`apply to each` f list MEANS
  CONSIDER list
  WHEN EMPTY THEN EMPTY
  WHEN x FOLLOWED BY xs THEN f x FOLLOWED BY `apply to each` f xs
```

Both give the function the same type.

## Type variables

- They are usually single lowercase letters (`a`, `b`, `c`), but any identifier works.
- They are in scope only in the type that follows.
- The names in one `FOR ALL` must be distinct: `FOR ALL a AND a` is an error.

## Not a quantifier over lists

`FOR ALL` works on types, not on values. To test every element of a list, or some element, use `all` or `any` from the [prelude](/l4/reference/libraries/prelude.md) with a [YIELD](/l4/reference/functions/YIELD.md) function:

```l4
IMPORT prelude

GIVEN amounts IS A LIST OF NUMBER
GIVETH A BOOLEAN
`every amount is positive` amounts MEANS all (GIVEN x YIELD x > 0) amounts
```

## In other languages

| L4                | Haskell       | TypeScript | Java     |
| ----------------- | ------------- | ---------- | -------- |
| `FOR ALL a`       | `forall a.`   | `<T>`      | `<T>`    |
| `FOR ALL a AND b` | `forall a b.` | `<T, U>`   | `<T, U>` |

## Related pages

- [GIVEN and GIVETH](/l4/reference/functions/GIVEN.md#type-parameters): type parameters of a defined function
- [ASSUME](/l4/reference/types/ASSUME.md): declare a name without a definition
- [DECLARE: Type expressions](/l4/reference/types/DECLARE.md#type-expressions): `FUNCTION FROM … TO …` and `TYPE`
- [prelude](/l4/reference/libraries/prelude.md): polymorphic standard functions
