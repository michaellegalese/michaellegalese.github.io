# ASSUME

`ASSUME` declares a name and its type without giving it a value or a definition.
Use it for facts that are supplied from outside, and for functions whose definition lives elsewhere.

## Syntax

```text
ASSUME name IS A Type
ASSUME name IS A FUNCTION FROM Type1 AND Type2 TO Type3
```

**Example file:** 

```l4-file
-- ASSUME keyword examples

-- Basic Variable Assumptions

-- Assume primitive types
ASSUME `is employed` IS A BOOLEAN
ASSUME `annual income` IS A NUMBER
ASSUME `applicant name` IS A STRING

-- Function Assumptions

-- Single parameter function
ASSUME `calculate tax` IS A FUNCTION FROM NUMBER TO NUMBER

-- The assumed function can be used in expressions
DECIDE `tax on income` IS `calculate tax` `annual income`

-- Using Assumed Values in Decisions

ASSUME age IS A NUMBER
ASSUME `has valid license` IS A BOOLEAN

-- Combine assumed values in logic
DECIDE `can drive` IS
  age >= 16 AND `has valid license`

-- Evaluating with Assumed Values

-- The expression uses assumed values
#CHECK `can drive`

-- Complex Example: Eligibility Check

ASSUME `applicant age` IS A NUMBER
ASSUME `years of residence` IS A NUMBER
ASSUME `has criminal record` IS A BOOLEAN

DECIDE `is eligible for benefit` IS
      `applicant age` >= 21
  AND `years of residence` >= 5
  AND NOT `has criminal record`

#CHECK `is eligible for benefit`
```



## Examples

```l4
ASSUME `the applicant's age` IS A NUMBER
ASSUME `the applicant is employed` IS A BOOLEAN
ASSUME `the tax on` IS A FUNCTION FROM NUMBER TO NUMBER

DECIDE `the applicant qualifies` IF
      `the applicant's age` >= 18
  AND `the applicant is employed`
```

An assumed name can be used anywhere its type fits, and the file type-checks.
Evaluating an expression that needs its value stops with "I could not continue evaluating, because I needed to know the value of … but it is an assumed term."
To try such a rule with values, give the facts as `GIVEN` inputs instead, or publish it with `@export` as below.

For a polymorphic assumed function, see [FOR ALL](/l4/reference/types/for-all.md).

## ASSUME and `@export`

When a function marked `@export` uses a top-level `ASSUME`, the assumed name becomes an input of the exported function, and the caller supplies it with the `GIVEN` inputs.
An `ASSUME` that no exported function uses stays as it is.

```l4
ASSUME `the applicant's age` IS A NUMBER
ASSUME `an unused fact` IS A BOOLEAN

@export Check whether the applicant is an adult
DECIDE `the applicant is an adult` IF `the applicant's age` >= 18
```

Here `` `the applicant's age` `` is an input of `` `the applicant is an adult` ``; `` `an unused fact` `` is not.

### Function-typed inputs are not supported for `@export`

An exported function cannot take an input of function type, whether it is a `GIVEN` input or an `ASSUME` it uses:

```text
ASSUME `the test` IS A FUNCTION FROM NUMBER TO BOOLEAN
@export Apply the test
DECIDE `passes` IF `the test` 42
```

`l4 check` reports "Function type inputs are not supported for @export", and the service refuses to deploy such a file.
A function cannot be sent as JSON over REST or MCP, and an assumed function has no definition to run.

## Related pages

- [GIVEN and GIVETH](/l4/reference/functions/GIVEN.md): inputs of a function
- [DECLARE](/l4/reference/types/DECLARE.md): types and type expressions
- [Deploy your rules](/l4/tutorials/deploying-rules/exporting-rules-for-deployment.md): `@export` in practice
