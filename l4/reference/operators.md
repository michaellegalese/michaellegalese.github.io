# Operators

Operators combine values into expressions: arithmetic, comparisons and logic.
Most have a word form, which reads well in legal text, and a symbol form; the two mean the same and can be mixed. Words suit rules that lawyers read; symbols are shorter.

## Pages

- [AND, OR, NOT, IMPLIES](/l4/reference/operators/logical.md): logical operators and their truth table
- [UNLESS](/l4/reference/operators/UNLESS.md): exceptions
- [Arithmetic](/l4/reference/operators/arithmetic.md): `PLUS`, `MINUS`, `TIMES`, `DIVIDED BY`, `MODULO`
- [Comparisons](/l4/reference/operators/comparisons.md): `EQUALS`, `GREATER THAN`, `AT LEAST` and the others

## Word and symbol forms

| Word         | Symbol | Word                    | Symbol |
| ------------ | ------ | ----------------------- | ------ |
| `PLUS`       | `+`    | `EQUALS`                | `=`    |
| `MINUS`      | `-`    | `GREATER THAN`, `ABOVE` | `>`    |
| `TIMES`      | `*`    | `LESS THAN`, `BELOW`    | `<`    |
| `DIVIDED BY` | `/`    | `AT LEAST`              | `>=`   |
| `MODULO`     |        | `AT MOST`               | `<=`   |
| `AND`        | `&&`   | `OR`                    | `\|\|` |
| `IMPLIES`    | `=>`   | `NOT`, `UNLESS`         |        |

**Example file:** 

```l4-file
-- Example: Textual vs symbolic operators
-- All forms are equivalent and can be mixed

age MEANS 25
citizen MEANS TRUE

-- All equivalent forms
eligible1 MEANS (age > 18) AND (citizen EQUALS TRUE)
eligible2 MEANS (age GREATER THAN 18) AND (citizen = TRUE)
eligible3 MEANS (age > 18) && (citizen EQUALS TRUE)

#EVAL eligible1
#EVAL eligible2
#EVAL eligible3
```



## Precedence

From the tightest-binding to the loosest:

| Level | Operators                                                                      | Grouping      |
| ----- | ------------------------------------------------------------------------------ | ------------- |
| 1     | function application, `'s`                                                     |               |
| 2     | `TIMES`, `DIVIDED BY`, `MODULO`                                                | left to right |
| 3     | `PLUS`, `MINUS`                                                                | left to right |
| 4     | `FOLLOWED BY`                                                                  | to the right  |
| 5     | `EQUALS`, `GREATER THAN`, `LESS THAN`, `AT LEAST`, `AT MOST` and their symbols |               |
| 6     | `AND`, `...`, `RAND`                                                           | to the right  |
| 7     | `OR`, `..`, `ROR`                                                              | to the right  |
| 8     | `IMPLIES`, `UNLESS`                                                            | to the right  |

- `2 + 3 * 4` is `14`, and `10 - 4 - 3` is `3`.
- A call of a user-defined infix function binds tighter than the operators in levels 2 to 8 of the table, so it needs no parentheses next to one.
  ``a AND n `exceeds by at least` x`` means ``a AND (n `exceeds by at least` x)``.
  For a function written `x SCALEDBY y`, `10 PLUS 2 SCALEDBY 3` means `10 PLUS (2 SCALEDBY 3)`.
  See [Mixfix functions](/l4/reference/functions.md#mixfix-functions).
- `NOT` is not in the table: it applies to everything after it on its line. See [NOT](/l4/reference/operators/logical.md#not).
- Parentheses override precedence: `(2 + 3) * 4` is `20`.
- When an expression spans several lines, an operator written further left than the lines above it applies to all of them together. See [Several lines](/l4/reference/operators/logical.md#several-lines).

**Example files:** 

```l4-file
-- Example: Operator precedence
-- Demonstrates how operators are evaluated in order

x MEANS 3
y MEANS 4
z MEANS 5

-- Multiplication before addition
#EVAL x PLUS y TIMES z
-- Evaluates as: 3 + (4 * 5) = 23

-- Use parentheses for clarity
#EVAL (x PLUS y) TIMES z
-- Evaluates as: 7 * 5 = 35
```

, 

```l4-file
-- Example: Operator associativity
-- Demonstrates left and right associative operators

-- Left-associative: subtraction
a MEANS 10
b MEANS 3
c MEANS 2

#EVAL a MINUS b MINUS c
-- Evaluates as: (10 - 3) - 2 = 5

-- Right-associative: list cons
#EVAL 1 FOLLOWED BY 2 FOLLOWED BY 3 FOLLOWED BY EMPTY
-- Evaluates as: 1 FOLLOWED BY (2 FOLLOWED BY (3 FOLLOWED BY EMPTY))
```



## Asyndetic AND and OR

Legal text often lists conditions with punctuation instead of "and" or "or". L4 has two such forms:

- `...` (three dots) means `AND`: `` `is a citizen` ... `is over 18` ... `has ID` ``
- `..` (two dots) means `OR`: `` approved .. pending .. `under review` ``

They have the same precedence as `AND` and `OR`.

**Example file:** 

```l4-file
-- Example: Asyndetic operators (... and ..)
-- Implicit AND and OR using punctuation

citizen MEANS TRUE
over18 MEANS TRUE
`has ID` MEANS TRUE

approved MEANS TRUE
pending MEANS FALSE
review MEANS FALSE

-- Ellipsis (...) = implicit AND
eligible1 MEANS citizen ... over18 ... `has ID`
-- Equivalent to: (citizen AND over18 AND has ID)

-- Double dot (..) = implicit OR
`in progress` MEANS approved .. pending .. review
-- Equivalent to: (approved OR pending OR review)

#EVAL eligible1
#EVAL `in progress`
```



## Other operators

- `FOLLOWED BY` adds an element to the front of a list: `1 FOLLOWED BY LIST 2, 3` is `LIST 1, 2, 3`.
- `'s` reads a field of a record: `` `the buyer`'s age ``. See [Syntax and directives: Genitive](/l4/reference/syntax.md#genitive).
- `%` after a number divides it by 100: `5%` is `0.05`.
- `AS STRING` converts a value to text; see [Coercions](/l4/reference/types/coercions.md).
- `CONCAT` joins text: `CONCAT "a", "b", "c"`. The [prelude](/l4/reference/libraries/prelude.md) adds `APPEND` (`"a" APPEND "b"`); see [Built-in functions](/l4/reference/builtins.md).
- `RAND` and `ROR` combine obligations; see [Deadlines and consequences](/l4/reference/regulative/consequences.md).
