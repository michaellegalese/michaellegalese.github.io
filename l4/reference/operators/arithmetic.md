# Arithmetic

The arithmetic operators work on `NUMBER`. Each has a word form and a symbol form, except `MODULO`, which has only the word.

| Word         | Symbol | Operation                | Example              |
| ------------ | ------ | ------------------------ | -------------------- |
| `PLUS`       | `+`    | addition                 | `5 PLUS 3` is `8`    |
| `MINUS`      | `-`    | subtraction              | `10 - 4` is `6`      |
| `TIMES`      | `*`    | multiplication           | `6 TIMES 7` is `42`  |
| `DIVIDED BY` | `/`    | division                 | `10 / 4` is `2.5`    |
| `MODULO`     |        | remainder after division | `17 MODULO 5` is `2` |

**Example file:** 

```l4-file
-- Arithmetic keyword examples

-- PLUS (Addition)

#EVAL 5 PLUS 3
#EVAL 5 + 3
#EVAL 10.5 + 2.5
#EVAL (-3) + 8

-- MINUS (Subtraction)

#EVAL 10 MINUS 4
#EVAL 10 - 4
#EVAL 5 - 8
#EVAL 0 - 5

-- TIMES (Multiplication)

#EVAL 6 TIMES 7
#EVAL 6 * 7
#EVAL 3.14 * 2
#EVAL (-3) * 4

-- DIVIDED BY (Division)

#EVAL 15 DIVIDED BY 3
#EVAL 15 / 3
#EVAL 10 / 4
#EVAL 7 / 2

-- MODULO (Remainder)

#EVAL 17 MODULO 5
#EVAL 10 MODULO 3
#EVAL 8 MODULO 2

-- Operator Precedence

-- Multiplication before addition
#EVAL 2 + 3 * 4

-- Parentheses override precedence
#EVAL (2 + 3) * 4

-- Division before subtraction
#EVAL 10 - 4 / 2

-- With parentheses
#EVAL (10 - 4) / 2

-- Complex Expressions

-- Circle area: pi * r^2
GIVEN r IS A NUMBER
`area of a circle` r MEANS 3.14159 TIMES r TIMES r

#EVAL `area of a circle` 5

-- Quadratic formula discriminant: b^2 - 4ac
GIVEN a IS A NUMBER
      b IS A NUMBER
      c IS A NUMBER
discriminant a b c MEANS
  b TIMES b MINUS 4 TIMES a TIMES c

#EVAL discriminant 1 5 6

-- Practical Examples

-- Calculate tax
GIVEN price IS A NUMBER
      `tax rate` IS A NUMBER
`tax on` price `tax rate` MEANS
  price TIMES `tax rate` / 100

#EVAL `tax on` 100 8.5

-- Calculate discount
GIVEN `original price` IS A NUMBER
      `discount percent` IS A NUMBER
`discounted price` `original price` `discount percent` MEANS
  `original price` MINUS (`original price` TIMES `discount percent` / 100)

#EVAL `discounted price` 200 25

-- Combining with Comparisons

ASSUME income IS A NUMBER
ASSUME expenses IS A NUMBER

DECIDE profit IS income MINUS expenses
DECIDE `is profitable` IS profit GREATER THAN 0

#CHECK `is profitable`
```



## Exact numbers

Arithmetic is exact: `(1 / 3) * 3 = 1` and `0.1 + 0.2 = 0.3` are both TRUE.
A result that has no finite decimal form, such as `1 / 3`, is shown rounded (`0.3333333333333333`), but the value itself is exact.

`MODULO` works on whole numbers only: `7 MODULO 2.5` stops evaluation with an error.
A negative number in an expression needs parentheses: `(-3) * 4`.

## Precedence

`TIMES`, `DIVIDED BY` and `MODULO` bind more tightly than `PLUS` and `MINUS`, and operators of the same level are applied from left to right:

```l4
#EVAL 2 + 3 * 4        -- 14
#EVAL (2 + 3) * 4      -- 20
#EVAL 10 - 4 - 3       -- 3
```

The full table, with comparisons and logical operators, is in [Operators: Precedence](/l4/reference/operators.md#precedence).

## More functions

Rounding (`ROUND`, `FLOOR`, `CEILING`, `TRUNC`) and functions such as `SQRT` are [built-in functions](/l4/reference/builtins.md). The [math](/l4/reference/libraries/math.md) library adds more.

## Related pages

- [Comparisons](/l4/reference/operators/comparisons.md)
- [Types](/l4/reference/types.md): `NUMBER`
