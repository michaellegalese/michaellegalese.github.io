# Comparisons

The comparison operators compare two values of the same type and give a BOOLEAN.

| Word                    | Symbol | TRUE when                     | Example             |
| ----------------------- | ------ | ----------------------------- | ------------------- |
| `EQUALS`                | `=`    | the values are equal          | `5 EQUALS 5`        |
| `GREATER THAN`, `ABOVE` | `>`    | the first is greater          | `10 GREATER THAN 5` |
| `LESS THAN`, `BELOW`    | `<`    | the first is smaller          | `3 BELOW 7`         |
| `AT LEAST`              | `>=`   | the first is greater or equal | `5 AT LEAST 5`      |
| `AT MOST`               | `<=`   | the first is smaller or equal | `4 AT MOST 10`      |

There is no "not equal" operator: write `NOT a EQUALS b`.

**Example file:** 

```l4-file
-- Comparison keyword examples

-- EQUALS

-- Numeric equality
#EVAL 5 EQUALS 5
#EVAL 5 = 5

-- String equality
#EVAL "hello" EQUALS "hello"
#EVAL "hello" = "world"

-- Boolean equality
#EVAL TRUE EQUALS TRUE
#EVAL FALSE = FALSE

-- GREATER THAN / ABOVE

-- Numeric greater than
#EVAL 10 GREATER THAN 5
#EVAL 10 > 5
#EVAL 10 ABOVE 5

-- String comparison (lexicographic)
#EVAL "b" GREATER THAN "a"
#EVAL "zebra" > "apple"

-- LESS THAN / BELOW

-- Numeric less than
#EVAL 3 LESS THAN 7
#EVAL 3 < 7
#EVAL 3 BELOW 7

-- AT LEAST (>=)

#EVAL 5 AT LEAST 5
#EVAL 6 AT LEAST 5
#EVAL 4 AT LEAST 5

#EVAL 5 >= 5
#EVAL 6 >= 5

-- AT MOST (<=)

#EVAL 4 AT MOST 10
#EVAL 10 AT MOST 10
#EVAL 11 AT MOST 10

#EVAL 4 <= 10
#EVAL 10 <= 10

-- Combined Comparisons

ASSUME age IS A NUMBER

DECIDE `is of working age` IS
      age AT LEAST 18
  AND age AT MOST 65

#CHECK `is of working age`

-- Comparison in Functions

GIVEN n IS A NUMBER
GIVETH A STRING
`classification of` n MEANS
  IF n LESS THAN 0
    THEN "negative"
    ELSE IF n EQUALS 0
           THEN "zero"
           ELSE "positive"

#EVAL `classification of` (-5)
#EVAL `classification of` 0
#EVAL `classification of` 42

-- Range Check Pattern

GIVEN val IS A NUMBER
      `minimum value` IS A NUMBER
      `maximum value` IS A NUMBER
GIVETH A BOOLEAN
`is in range` val `minimum value` `maximum value` MEANS
  val AT LEAST `minimum value` AND val AT MOST `maximum value`

#EVAL `is in range` 5 1 10
#EVAL `is in range` 15 1 10
#EVAL `is in range` 1 1 10
```



## Types

- `EQUALS` works on numbers, text, booleans, values of enumerations, records and lists.
- The ordering operators work on `NUMBER`, `STRING` (in character-code order, so `"B" < "a"`) and `BOOLEAN` (`FALSE` is less than `TRUE`).
- Dates can be compared once the [daydate](/l4/reference/libraries/daydate.md) library is imported.

## Combining comparisons

Comparisons bind more tightly than `AND` and `OR`, so no parentheses are needed:

```l4
GIVEN age IS A NUMBER
      income IS A NUMBER
GIVETH A BOOLEAN
`is eligible` age income MEANS
      age AT LEAST 18
  AND age AT MOST 65
  AND income GREATER THAN 30000
```

See [Operators: Precedence](/l4/reference/operators.md#precedence).

## Related pages

- [AND, OR, NOT, IMPLIES](/l4/reference/operators/logical.md)
- [Arithmetic](/l4/reference/operators/arithmetic.md)
- [IF](/l4/reference/control-flow/IF.md)
