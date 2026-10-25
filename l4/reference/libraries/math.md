# math

Exponentials, logarithms, square roots and trigonometry. Import it with `IMPORT math`.

The functions are lowercase wrappers around the [built-in](/l4/reference/builtins.md#numbers) `LN`, `SQRT`, `ASIN` and so on. Where the built-in stops with an error on bad input, the wrapper gives `NOTHING`, so the result is a `MAYBE NUMBER`.

**Example:** 

```l4-file
-- Math Example: Mathematical functions with safe error handling
IMPORT math

-- Euler's constant
#EVAL EULER  -- 2.718281828459045

-- Exponential function
DECIDE `exponential growth` IS exp 2
#EVAL `exponential growth`  -- e^2 ≈ 7.389

-- Safe logarithms (return MAYBE to handle domain errors)
DECIDE `valid natural log` IS ln 10
#EVAL `valid natural log`  -- JUST 2.302...

DECIDE `invalid natural log` IS ln (0 MINUS 5)
#EVAL `invalid natural log`  -- NOTHING (negative input)

DECIDE `base 10 logarithm` IS log10 100
#EVAL `base 10 logarithm`  -- JUST 2

-- Safe square root
DECIDE `valid square root` IS sqrt 16
#EVAL `valid square root`  -- JUST 4

DECIDE `invalid square root` IS sqrt (0 MINUS 4)
#EVAL `invalid square root`  -- NOTHING (negative input)

-- Absolute value
DECIDE `absolute of positive` IS absNumber 42
#EVAL `absolute of positive`  -- 42

DECIDE `absolute of negative` IS absNumber (0 MINUS 42)
#EVAL `absolute of negative`  -- 42

-- Trigonometric functions (angles in radians)
DECIDE `sine of zero` IS sin 0
#EVAL `sine of zero`  -- JUST 0

DECIDE `cosine of zero` IS cos 0
#EVAL `cosine of zero`  -- JUST 1

-- Tangent with asymptote protection
DECIDE `tangent of zero` IS tan 0
#EVAL `tangent of zero`  -- JUST 0

-- Inverse trig with domain checking
DECIDE `valid arcsine` IS asin 0.5
#EVAL `valid arcsine`  -- JUST 0.523... (π/6)

DECIDE `invalid arcsine` IS asin 2
#EVAL `invalid arcsine`  -- NOTHING (|x| > 1)

-- Using fromMaybe to extract with default
DECIDE `safe extracted value` IS fromMaybe 0 (sqrt 25)
#EVAL `safe extracted value`  -- 5

DECIDE `value with default` IS fromMaybe 0 (sqrt (0 MINUS 1))
#EVAL `value with default`  -- 0 (used default)
```



## Constants

| Constant      | Value                                                                  |
| ------------- | ---------------------------------------------------------------------- |
| `EULER`       | Euler's number, 2.718281828459045                                      |
| `TAN_EPSILON` | 0.000000001, the threshold below which `tan` treats the cosine as zero |

## Functions

| Function    | Input       | Gives        | What it does                                       |
| ----------- | ----------- | ------------ | -------------------------------------------------- |
| `exp`       | `x`: NUMBER | NUMBER       | e to the power `x`                                 |
| `ln`        | `x`: NUMBER | MAYBE NUMBER | Natural logarithm; `NOTHING` if `x` is 0 or less   |
| `log10`     | `x`: NUMBER | MAYBE NUMBER | Base-10 logarithm; `NOTHING` if `x` is 0 or less   |
| `sqrt`      | `x`: NUMBER | MAYBE NUMBER | Square root; `NOTHING` if `x` is negative          |
| `absNumber` | `x`: NUMBER | NUMBER       | Absolute value                                     |
| `sin`       | `x`: NUMBER | MAYBE NUMBER | Sine; always `JUST`                                |
| `cos`       | `x`: NUMBER | MAYBE NUMBER | Cosine; always `JUST`                              |
| `tan`       | `x`: NUMBER | MAYBE NUMBER | Tangent; `NOTHING` where the cosine is almost zero |
| `asin`      | `x`: NUMBER | MAYBE NUMBER | Arc sine; `NOTHING` outside -1 to 1                |
| `acos`      | `x`: NUMBER | MAYBE NUMBER | Arc cosine; `NOTHING` outside -1 to 1              |
| `atan`      | `x`: NUMBER | MAYBE NUMBER | Arc tangent; always `JUST`                         |

Angles are in radians.
