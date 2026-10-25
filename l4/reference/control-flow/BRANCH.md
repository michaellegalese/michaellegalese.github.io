# BRANCH

`BRANCH` chooses between several values by a list of conditions. The conditions are tried from top to bottom, and the first one that holds gives the result. `OTHERWISE` gives the result when none holds.

## Syntax

```text
BRANCH IF condition1 THEN value1
       IF condition2 THEN value2
       OTHERWISE default-value

BRANCH
  IF condition1 THEN value1
  IF condition2 THEN value2
  OTHERWISE default-value
```

- Each `IF … THEN` and the `OTHERWISE` must be indented further than the `BRANCH` keyword. Put them in one column, either after `BRANCH` on its line or on the lines below.
- `OTHERWISE` is required.
- Every value must have the same type.

**Example file:** 

```l4-file
-- BRANCH examples

-- The conditions are tried from top to bottom; the first that holds gives the result.

DECLARE Category IS ONE OF Standard, Premium, Enterprise

GIVEN category IS A Category
GIVETH A NUMBER
`monthly price for` category MEANS
  BRANCH IF category EQUALS Standard   THEN 10
         IF category EQUALS Premium    THEN 25
         IF category EQUALS Enterprise THEN 100
         OTHERWISE 0

#EVAL `monthly price for` Premium

-- The IF lines can also start on the line below BRANCH,
-- indented further than BRANCH itself.

GIVEN `annual income` IS A NUMBER
GIVETH A NUMBER
`tax rate for` `annual income` MEANS
  BRANCH
    IF `annual income` > 120000 THEN 0.4
    IF `annual income` > 50000  THEN 0.3
    IF `annual income` > 20000  THEN 0.2
    OTHERWISE 0

-- 150000 is above 50000 too, but the first condition that holds wins.
#EVAL `tax rate for` 150000
#EVAL `tax rate for` 60000
#EVAL `tax rate for` 10000
```



## Example

```l4
GIVEN `annual income` IS A NUMBER
GIVETH A NUMBER
`tax rate for` `annual income` MEANS
  BRANCH
    IF `annual income` > 120000 THEN 0.4
    IF `annual income` > 50000  THEN 0.3
    IF `annual income` > 20000  THEN 0.2
    OTHERWISE 0
```

`` `tax rate for` 150000 `` gives `0.4`: 150000 is above 50000 as well, but the first condition that holds wins.

## BRANCH, IF or CONSIDER

| Situation                                                                      | Use                     |
| ------------------------------------------------------------------------------ | ----------------------- |
| one condition, two outcomes                                                    | [IF](/l4/reference/control-flow/IF.md)             |
| several conditions, tried in order                                             | `BRANCH`                |
| the value's shape: enumeration values, `JUST`/`NOTHING`, list or record fields | [CONSIDER](/l4/reference/control-flow/CONSIDER.md) |

`BRANCH` gives the same result as `IF … ELSE IF … ELSE`, without nesting each `IF` deeper.

## Related pages

- [IF](/l4/reference/control-flow/IF.md)
- [CONSIDER](/l4/reference/control-flow/CONSIDER.md)
- [Control flow](/l4/reference/control-flow.md)
