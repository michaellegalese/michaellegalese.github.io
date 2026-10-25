# AKA

`AKA` ("also known as") gives a function, a value, a type or a section more than one name. Every name refers to the same thing.

## Syntax

```text
name AKA alias1, alias2 MEANS expression
DECLARE TypeName AKA Alias HAS ...
DECLARE TypeName AKA Alias IS ...
§ `Section name` AKA alias
```

**Example file:** 

```l4-file
-- AKA keyword examples

-- Value Aliases

-- Define a value with multiple names
x AKA y, z MEANS TRUE

-- All names evaluate to the same value
#EVAL x
#EVAL y
#EVAL z

-- Type Aliases

-- Create an alias for BOOLEAN
DECLARE B AKA `Yes or no` IS BOOLEAN

-- Use either name in type signatures
GIVETH B
example1 MEANS TRUE

GIVETH `Yes or no`
example2 MEANS FALSE

#EVAL example1
#EVAL example2

-- Record Type with Alias

GIVEN a IS A TYPE
      b IS A TYPE
DECLARE Pair OF a, b AKA `2-tuple`
  HAS
    px IS AN a
    py IS A b

-- Use the primary name
GIVETH Pair OF NUMBER, NUMBER
`first pair` MEANS Pair WITH px IS 3, py IS 5

-- Use the alias
GIVETH `2-tuple` OF NUMBER, NUMBER
`second pair` MEANS Pair WITH px IS 10, py IS 20

#EVAL `first pair`
#EVAL `second pair`

-- Legal Terminology Example

-- Primary legal term with common alias
DECLARE `Authorized Representative` AKA Agent
  HAS
    name IS A STRING
    `licence number` IS A STRING

-- Can construct using either name
DECIDE `the agent` IS `Authorized Representative` WITH
  name IS "John Smith",
  `licence number` IS "A12345"

-- Access works with both names
#EVAL `the agent`'s name

-- Function with Alias

GIVEN n IS A NUMBER
double AKA twice MEANS n TIMES 2

-- Both names work
#EVAL double 5
#EVAL twice 5

-- Multiple Aliases

GIVEN amount IS A NUMBER
tax AKA VAT, `sales tax`, GST MEANS amount TIMES 0.1

#EVAL tax 100
#EVAL VAT 100
#EVAL `sales tax` 100
#EVAL GST 100
```



## Values

```l4
`the borrower` AKA `the debtor` MEANS "Acme Ltd"

#EVAL `the debtor`
```

## Types

A statute and the people who apply it often use different words for the same thing:

```l4
DECLARE `Authorised Representative` AKA Agent
  HAS
    name IS A STRING
    `licence number` IS A STRING

GIVEN agent IS AN Agent
GIVETH A STRING
`licence of` agent MEANS agent's `licence number`
```

A type synonym can have aliases too: ``DECLARE `Value Added Tax rate` AKA `VAT rate` IS NUMBER``.

## Sections

An alias on a section heading gives a shorter name for qualified references:

```l4
§ `Definitions for Part VII` AKA defs
`taxable income` MEANS 50000

#EVAL defs's `taxable income`
```

See [Syntax and directives: Sections](/l4/reference/syntax.md#sections).

## Related pages

- [DECLARE](/l4/reference/types/DECLARE.md): type declarations
- [DECIDE](/l4/reference/functions/DECIDE.md) and [MEANS](/l4/reference/functions/MEANS.md): value definitions
