# Module 2: Legal entities and relationships

In this module, you'll learn how to model structured legal entities using L4's type system.

## Learning Objectives

By the end of this module, you will be able to:

- Define record types with DECLARE and HAS
- Create enumeration types with IS ONE OF
- Model relationships between entities
- Access fields using the possessive syntax
- Construct record values

---

## From Strings to Structured Types

### The Problem with Strings

Using simple text leads to errors and ambiguity. You can't distinguish a charity from a person, access parts (name, address, etc.), or detect typos.

### Structured Types: The Solution

The complete working example:



```l4-file
-- Module 2: Legal Entities and Relationships - Complete Examples
-- All examples are validated and use natural language identifiers

-- =============================================================================
-- SECTION 1: Basic Record Types
-- =============================================================================

-- A person with natural language field names
DECLARE Person
    HAS `the person's name` IS A STRING
        `the person's address` IS A STRING
        `the person is a governor` IS A BOOLEAN

-- A registered charity
DECLARE `Registered Charity`
    HAS `the charity's name` IS A STRING
        `the registration number` IS A STRING
        `the registration date` IS A NUMBER
        `the address` IS A STRING

-- =============================================================================
-- SECTION 2: Enumeration Types
-- =============================================================================

-- Charitable purposes from legislation
DECLARE Purpose IS ONE OF
    `prevention or relief of poverty`
    `advancement of education`
    `advancement of religion`
    `advancement of health`
    `advancement of animal welfare`
    `other purpose` HAS `the description` IS A STRING

-- Status of a charity
DECLARE Status IS ONE OF
    Active
    Suspended HAS `the reason` IS A STRING
    Deregistered

-- =============================================================================
-- SECTION 3: Nested Types
-- =============================================================================

DECLARE `Bank Account`
    HAS `the bank name` IS A STRING
        `the account number` IS A STRING
        `the SWIFT code` IS A STRING

DECLARE Company
    HAS `the company name` IS A STRING
        `the jurisdiction` IS A STRING
        `the bank account` IS A `Bank Account`

-- =============================================================================
-- SECTION 4: Union Types (Legal Entity)
-- =============================================================================

DECLARE `Legal Entity` IS ONE OF
    Individual HAS `the person` IS A Person
    Corporation HAS `the company` IS A Company

-- =============================================================================
-- SECTION 5: Pattern Matching
-- =============================================================================

-- Check if a purpose is charitable
GIVEN purpose IS A Purpose
GIVETH A BOOLEAN
DECIDE `the purpose is charitable` IS
    CONSIDER purpose
    WHEN `advancement of education` THEN TRUE
    WHEN `advancement of animal welfare` THEN TRUE
    WHEN `prevention or relief of poverty` THEN TRUE
    WHEN `advancement of health` THEN TRUE
    WHEN `advancement of religion` THEN TRUE
    WHEN `other purpose` description THEN FALSE
    OTHERWISE FALSE

-- Get the name of a legal entity
GIVEN entity IS A `Legal Entity`
GIVETH A STRING
`the entity's name` MEANS
    CONSIDER entity
    WHEN Individual person THEN person's `the person's name`
    WHEN Corporation company THEN company's `the company name`

-- =============================================================================
-- SECTION 6: Test Data
-- =============================================================================

-- Create test persons
`Alice Smith` MEANS Person "Alice Smith" "123 Main Street, Jersey" TRUE
`Bob Jones` MEANS Person "Bob Jones" "456 High Street, Jersey" FALSE

-- Create test purposes
`education purpose` MEANS `advancement of education`
`custom purpose` MEANS `other purpose` "local community support"

-- Create test company
`Acme Bank Account` MEANS `Bank Account` "DBS Bank" "123-456789-0" "DBSSSGSG"
`Acme Corporation` MEANS Company "Acme Corp" "Singapore" `Acme Bank Account`

-- Create test legal entities
`Alice as individual` MEANS Individual `Alice Smith`
`Acme as corporation` MEANS Corporation `Acme Corporation`

-- =============================================================================
-- SECTION 7: Tests
-- =============================================================================

-- Field access tests
#EVAL `Alice Smith`'s `the person's name`
#EVAL `Alice Smith`'s `the person is a governor`

-- Nested field access
#EVAL `Acme Corporation`'s `the bank account`'s `the bank name`

-- Pattern matching tests
#EVAL `the purpose is charitable` `education purpose`
#EVAL `the purpose is charitable` `custom purpose`

-- Legal entity name extraction
#EVAL `the entity's name` `Alice as individual`
#EVAL `the entity's name` `Acme as corporation`
```



```l4
DECLARE `Registered Charity`
    HAS `the charity's name` IS A STRING
        `the registration number` IS A STRING
        `the registration date` IS A DATE
        `the charity's address` IS A STRING
        `the charity's purposes` IS A LIST OF Purpose
```

Benefits of structured types:

- **Prevents errors:** Can't use a person where you need a charity
- **Captures relationships:** Links charities to their purposes, addresses
- **Enables validation:** L4 checks if all required information is present

---

## Declaring Record Types

Use `DECLARE` with `HAS` to define record types:

```l4
DECLARE Person
    HAS `the person's name` IS A STRING
        `the person's age` IS A NUMBER
        `the person is an adult` IS A BOOLEAN
```

### Syntax Rules

1. **Indentation matters:** All fields must be indented consistently under `HAS`
2. **Each field on its own line** (or separated by commas)
3. **Field names must be unique** within the type
4. **Use natural language field names** with backticks: `` `the person's name` ``

### Creating Record Values

Use the type name followed by values in field order:

```l4
alice MEANS Person "Alice" 30 TRUE
```

---

## Enumeration Types

### The Problem with Free Text

Using strings for categories allows mistakes—typos and inconsistencies go undetected.

### Enumerations: The Solution

Use `IS ONE OF` to define exact legal categories:

```l4
DECLARE Purpose IS ONE OF
    `prevention or relief of poverty`
    `advancement of education`
    `advancement of religion`
    `advancement of health`
    `advancement of animal welfare`
```

Now only these exact values are allowed—typos are compile-time errors!

### Enumerations with Data

Some enum variants can carry additional data:

```l4
DECLARE Purpose IS ONE OF
    `advancement of education`
    `advancement of health`
    `other purpose` HAS `the description` IS A STRING
```

The `other purpose` variant includes a description for edge cases.

---

## Pattern Matching on Enumerations

Use `CONSIDER` to handle different variants:

```l4
GIVEN purpose IS A Purpose
GIVETH A BOOLEAN
DECIDE `the purpose is charitable` IS
    CONSIDER purpose
    WHEN `advancement of education` THEN TRUE
    WHEN `advancement of health` THEN TRUE
    WHEN `other purpose` description THEN FALSE
```

### CONSIDER Syntax

The general pattern is:

- `CONSIDER expression`
- `WHEN pattern THEN result` (one for each variant)
- `OTHERWISE default result` (optional catch-all)

---

## Connecting Multiple Entities

Legal systems involve relationships between entities. A field can reference another type, creating relationships.

See the `Company` type in 

```l4-file
-- Module 2: Legal Entities and Relationships - Complete Examples
-- All examples are validated and use natural language identifiers

-- =============================================================================
-- SECTION 1: Basic Record Types
-- =============================================================================

-- A person with natural language field names
DECLARE Person
    HAS `the person's name` IS A STRING
        `the person's address` IS A STRING
        `the person is a governor` IS A BOOLEAN

-- A registered charity
DECLARE `Registered Charity`
    HAS `the charity's name` IS A STRING
        `the registration number` IS A STRING
        `the registration date` IS A NUMBER
        `the address` IS A STRING

-- =============================================================================
-- SECTION 2: Enumeration Types
-- =============================================================================

-- Charitable purposes from legislation
DECLARE Purpose IS ONE OF
    `prevention or relief of poverty`
    `advancement of education`
    `advancement of religion`
    `advancement of health`
    `advancement of animal welfare`
    `other purpose` HAS `the description` IS A STRING

-- Status of a charity
DECLARE Status IS ONE OF
    Active
    Suspended HAS `the reason` IS A STRING
    Deregistered

-- =============================================================================
-- SECTION 3: Nested Types
-- =============================================================================

DECLARE `Bank Account`
    HAS `the bank name` IS A STRING
        `the account number` IS A STRING
        `the SWIFT code` IS A STRING

DECLARE Company
    HAS `the company name` IS A STRING
        `the jurisdiction` IS A STRING
        `the bank account` IS A `Bank Account`

-- =============================================================================
-- SECTION 4: Union Types (Legal Entity)
-- =============================================================================

DECLARE `Legal Entity` IS ONE OF
    Individual HAS `the person` IS A Person
    Corporation HAS `the company` IS A Company

-- =============================================================================
-- SECTION 5: Pattern Matching
-- =============================================================================

-- Check if a purpose is charitable
GIVEN purpose IS A Purpose
GIVETH A BOOLEAN
DECIDE `the purpose is charitable` IS
    CONSIDER purpose
    WHEN `advancement of education` THEN TRUE
    WHEN `advancement of animal welfare` THEN TRUE
    WHEN `prevention or relief of poverty` THEN TRUE
    WHEN `advancement of health` THEN TRUE
    WHEN `advancement of religion` THEN TRUE
    WHEN `other purpose` description THEN FALSE
    OTHERWISE FALSE

-- Get the name of a legal entity
GIVEN entity IS A `Legal Entity`
GIVETH A STRING
`the entity's name` MEANS
    CONSIDER entity
    WHEN Individual person THEN person's `the person's name`
    WHEN Corporation company THEN company's `the company name`

-- =============================================================================
-- SECTION 6: Test Data
-- =============================================================================

-- Create test persons
`Alice Smith` MEANS Person "Alice Smith" "123 Main Street, Jersey" TRUE
`Bob Jones` MEANS Person "Bob Jones" "456 High Street, Jersey" FALSE

-- Create test purposes
`education purpose` MEANS `advancement of education`
`custom purpose` MEANS `other purpose` "local community support"

-- Create test company
`Acme Bank Account` MEANS `Bank Account` "DBS Bank" "123-456789-0" "DBSSSGSG"
`Acme Corporation` MEANS Company "Acme Corp" "Singapore" `Acme Bank Account`

-- Create test legal entities
`Alice as individual` MEANS Individual `Alice Smith`
`Acme as corporation` MEANS Corporation `Acme Corporation`

-- =============================================================================
-- SECTION 7: Tests
-- =============================================================================

-- Field access tests
#EVAL `Alice Smith`'s `the person's name`
#EVAL `Alice Smith`'s `the person is a governor`

-- Nested field access
#EVAL `Acme Corporation`'s `the bank account`'s `the bank name`

-- Pattern matching tests
#EVAL `the purpose is charitable` `education purpose`
#EVAL `the purpose is charitable` `custom purpose`

-- Legal entity name extraction
#EVAL `the entity's name` `Alice as individual`
#EVAL `the entity's name` `Acme as corporation`
```

 which has a `Bank Account` field.

The `elem` function (from prelude) checks if an item is in a list.

---

## Field Access

### Possessive Syntax

Use `'s` to access record fields. Using the `Registered Charity` type declared above:

```l4
charity's `the charity's name`        -- the name field
charity's `the registration number`   -- the registration number field
charity's `the charity's purposes`    -- the list of purposes
```

### Chained Access

Access nested fields by chaining:

```l4
company's `the bank account`'s `the bank name`
-- Gets the bank name field of the bank account field of company
```

### Function Application Alternative

You can also write field access as function application:

```l4
`the charity's name` charity    -- same as: charity's `the charity's name`
```

This is useful in complex expressions.

---

## Nested Types

Types can contain other types:

```l4
DECLARE `Bank Account`
    HAS `the bank name` IS A STRING
        `the account number` IS A STRING

DECLARE Company
    HAS `the company name` IS A STRING
        `the bank account` IS A `Bank Account`
```

---

## Union Types for Alternatives

When something can be one of several different types:

```l4
DECLARE `Legal Entity` IS ONE OF
    Individual HAS `the person` IS A Person
    Corporation HAS `the company` IS A Company
```

A `Legal Entity` can be an individual or corporation—each with different data.

### Using Union Types

```l4
GIVEN entity IS A `Legal Entity`
GIVETH A STRING
`the entity's name` MEANS
    CONSIDER entity
    WHEN Individual person THEN person's `the person's name`
    WHEN Corporation company THEN company's `the company name`
```

---

## Real-World Example: Charity Registration

For a complete charity registration system with types, see 

```l4-file
-- Module 7: Putting It Together - Capstone Example
-- A complete charity registration system
-- All examples are validated and use natural language identifiers

IMPORT prelude

-- =============================================================================
-- SECTION 1: Type Definitions
-- =============================================================================

-- Charitable purposes (from legislation)
DECLARE Purpose IS ONE OF
    `prevention or relief of poverty`
    `advancement of education`
    `advancement of religion`
    `advancement of health`
    `advancement of animal welfare`
    `other purpose` HAS `the description` IS A STRING

-- Legal status of a charity
DECLARE Status IS ONE OF
    Active
    Suspended HAS `the reason` IS A STRING
    Deregistered

-- Criminal conviction record
DECLARE Conviction
    HAS `the description` IS A STRING
        `the conviction is spent` IS A BOOLEAN

-- Governor of a charity
DECLARE Governor
    HAS `the governor's name` IS A STRING
        `the governor's age` IS A NUMBER
        `the governor is bankrupt` IS A BOOLEAN
        `the governor's convictions` IS A LIST OF Conviction

-- The main charity record
DECLARE `Registered Charity`
    HAS `the charity's name` IS A STRING
        `the registration number` IS A STRING
        `the charity's status` IS A Status
        `the charity's purposes` IS A LIST OF Purpose
        `the charity's governors` IS A LIST OF Governor

-- =============================================================================
-- SECTION 2: Eligibility Rules
-- =============================================================================

-- A governor must be an adult (at least 18)
GIVEN governor IS A Governor
GIVETH A BOOLEAN
DECIDE `the governor is an adult` IF
    governor's `the governor's age` >= 18

-- Check for disqualifying convictions (unspent convictions)
GIVEN governor IS A Governor
GIVETH A BOOLEAN
DECIDE `the governor has a disqualifying conviction` IF
    any (GIVEN c YIELD c's `the conviction is spent` EQUALS FALSE) (governor's `the governor's convictions`)

-- A person can be a governor if they meet all criteria
GIVEN governor IS A Governor
GIVETH A BOOLEAN
DECIDE `the person can be a governor` IF
    `the governor is an adult` governor
    AND NOT governor's `the governor is bankrupt`
    AND NOT `the governor has a disqualifying conviction` governor

-- A purpose is charitable if it's from the approved list
GIVEN purpose IS A Purpose
GIVETH A BOOLEAN
DECIDE `the purpose is charitable` IS
    CONSIDER purpose
    WHEN `prevention or relief of poverty` THEN TRUE
    WHEN `advancement of education` THEN TRUE
    WHEN `advancement of religion` THEN TRUE
    WHEN `advancement of health` THEN TRUE
    WHEN `advancement of animal welfare` THEN TRUE
    WHEN `other purpose` description THEN FALSE

-- A charity is valid if it has valid purposes and valid governors
GIVEN charity IS A `Registered Charity`
GIVETH A BOOLEAN
DECIDE `the charity is valid` IF
    charity's `the charity's status` EQUALS Active
    AND NOT null (charity's `the charity's purposes`)
    AND all (GIVEN p YIELD `the purpose is charitable` p) (charity's `the charity's purposes`)
    AND NOT null (charity's `the charity's governors`)
    AND all (GIVEN g YIELD `the person can be a governor` g) (charity's `the charity's governors`)

-- =============================================================================
-- SECTION 3: Classification and Fees
-- =============================================================================

-- Classify a charity by its annual income (multi-way decision with BRANCH)
GIVEN `the annual income` IS A NUMBER
GIVETH A STRING
DECIDE `the charity's size band` IS
    BRANCH
        IF `the annual income` < 10000 THEN "small"
        IF `the annual income` < 500000 THEN "medium"
        OTHERWISE "large"

-- The annual filing fee, decomposed with WHERE
GIVEN charity IS A `Registered Charity`
      `the annual income` IS A NUMBER
GIVETH A NUMBER
`the annual filing fee` MEANS
    `the base fee` + `the governor levy`
    WHERE
        `the base fee` MEANS
            BRANCH
                IF `the charity's size band` `the annual income` EQUALS "small" THEN 50
                IF `the charity's size band` `the annual income` EQUALS "medium" THEN 200
                OTHERWISE 500

        `the governor levy` MEANS
            10 * count (charity's `the charity's governors`)

-- =============================================================================
-- SECTION 4: Filing Obligations
-- =============================================================================

-- Actors and actions for the regulatory system
DECLARE Actor IS ONE OF
    `the Charity`
    `the Commissioner`

DECLARE Action IS ONE OF
    `file the annual return`
    `issue a Required Steps Notice`
    `correct the deficiencies`
    `distribute profits to the governors`

-- Annual return filing obligation
GIVEN charity IS A `Registered Charity`
GIVETH A DEONTIC Actor Action
`the annual return obligation` MEANS
    IF charity's `the charity's status` EQUALS Active
    THEN
        PARTY `the Charity`
        MUST `file the annual return`
        WITHIN 60
        HENCE FULFILLED
        LEST
            PARTY `the Commissioner`
            MUST `issue a Required Steps Notice`
            WITHIN 14
            HENCE `the correction period`
            LEST BREACH BY `the Commissioner` BECAUSE "failed to issue notice"
    ELSE FULFILLED

-- After notice is issued, charity has time to correct
GIVETH A DEONTIC Actor Action
`the correction period` MEANS
    PARTY `the Charity`
    MUST `correct the deficiencies`
    WITHIN 30
    HENCE FULFILLED
    LEST BREACH BY `the Charity` BECAUSE "failed to correct after notice"

-- A prohibition: charities must not distribute profits to their governors
GIVETH A DEONTIC Actor Action
`the profit distribution prohibition` MEANS
    PARTY `the Charity`
    SHANT `distribute profits to the governors`
    HENCE FULFILLED
    LEST BREACH BY `the Charity` BECAUSE "distributed profits to governors"

-- =============================================================================
-- SECTION 5: Test Data
-- =============================================================================

-- Valid governor
`Jane Smith` MEANS Governor "Jane Smith" 45 FALSE (LIST)

-- Governor with issues
`John Doe (bankrupt)` MEANS Governor "John Doe" 50 TRUE (LIST)

`unspent conviction` MEANS Conviction "Fraud conviction 2020" FALSE
`Bob Jones (with conviction)` MEANS Governor "Bob Jones" 40 FALSE (LIST `unspent conviction`)

`Young Person` MEANS Governor "Young Person" 16 FALSE (LIST)

-- Valid charity
`Jersey Animal Welfare` MEANS `Registered Charity` "Jersey Animal Welfare" "CH001" Active (LIST `advancement of animal welfare`, `advancement of education`) (LIST `Jane Smith`)

-- Charity with invalid governor
`Problem Charity` MEANS `Registered Charity` "Problem Charity" "CH002" Active (LIST `advancement of education`) (LIST `John Doe (bankrupt)`)

-- Suspended charity
`Suspended Charity` MEANS `Registered Charity` "Suspended Charity" "CH003" (Suspended "Financial irregularities") (LIST `advancement of health`) (LIST `Jane Smith`)

-- =============================================================================
-- SECTION 6: Tests
-- =============================================================================

-- Governor eligibility tests
#EVAL `the governor is an adult` `Jane Smith`
#EVAL `the governor is an adult` `Young Person`
#EVAL `the person can be a governor` `Jane Smith`
#EVAL `the person can be a governor` `John Doe (bankrupt)`
#EVAL `the person can be a governor` `Bob Jones (with conviction)`
#EVAL `the person can be a governor` `Young Person`

-- Charity validity tests
#EVAL `the charity is valid` `Jersey Animal Welfare`
#EVAL `the charity is valid` `Problem Charity`
#EVAL `the charity is valid` `Suspended Charity`

-- Classification and fee tests
#EVAL `the charity's size band` 5000
#EVAL `the charity's size band` 60000
#EVAL `the charity's size band` 750000
#EVAL `the annual filing fee` `Jersey Animal Welfare` 60000

-- Test filing obligation scenarios

-- Happy path: charity files on time
#TRACE `the annual return obligation` `Jersey Animal Welfare` AT 0 WITH
    PARTY `the Charity` DOES `file the annual return` AT 30

-- Suspended charity: no filing required
#TRACE `the annual return obligation` `Suspended Charity` AT 0 WITH

-- Late filing: the charity misses the 60-day deadline (its day-65 filing
-- arrives too late), the Commissioner issues a notice, then the charity
-- corrects within the correction period
#TRACE `the annual return obligation` `Jersey Animal Welfare` AT 0 WITH
    PARTY `the Charity` DOES `file the annual return` AT 65
    PARTY `the Commissioner` DOES `issue a Required Steps Notice` AT 70
    PARTY `the Charity` DOES `correct the deficiencies` AT 90

-- Prohibition: distributing profits is a breach
#TRACE `the profit distribution prohibition` AT 0 WITH
    PARTY `the Charity` DOES `distribute profits to the governors` AT 10
```

 in the capstone module.

---

## Common Mistakes

### 1. Inconsistent Indentation

```l4
-- Wrong: Fields not aligned
DECLARE Person
    HAS name IS A STRING
      age IS A NUMBER

-- Right: All fields aligned
DECLARE Person
    HAS name IS A STRING
        age IS A NUMBER
```

### 2. Missing Field Values

```l4
-- Wrong: Missing age field
alice MEANS Person "Alice" TRUE

-- Right: Provide all fields
alice MEANS Person "Alice" 30 TRUE
```

---

## Exercises

### Exercise 1: Define a Contract Type

Define types for a simple contract with parties and an amount.

<details>
<summary>Solution</summary>

```l4
DECLARE Contract
    HAS `the first party` IS A STRING
        `the second party` IS A STRING
        `the contract amount` IS A NUMBER

`the consulting contract` MEANS Contract "Acme Corp" "Jane Smith" 5000

#EVAL `the consulting contract`'s `the contract amount`
```

</details>

### Exercise 2: Employment Status Enumeration

Define an enumeration for employment status (full-time, part-time, contractor, terminated).

<details>
<summary>Solution</summary>

```l4
DECLARE `Employment Status` IS ONE OF
    `full time`
    `part time`
    contractor
    terminated
```

</details>

### Exercise 3: Pattern Matching

Write a function that returns a person's employment description based on their status.

<details>
<summary>Solution</summary>

```l4
GIVEN status IS A `Employment Status`
GIVETH A STRING
`the employment description` MEANS
    CONSIDER status
    WHEN `full time` THEN "employed full time"
    WHEN `part time` THEN "employed part time"
    WHEN contractor THEN "engaged as a contractor"
    WHEN terminated THEN "no longer employed"

#EVAL `the employment description` `full time`
#EVAL `the employment description` contractor
```

</details>

---

## Summary

| Concept               | Syntax                                             |
| --------------------- | -------------------------------------------------- |
| Record type           | `DECLARE Type HAS field IS A FieldType`            |
| Enumeration           | `DECLARE Type IS ONE OF Variant1, Variant2`        |
| Enum with data        | `Variant HAS field IS A Type`                      |
| Create record         | `Type value1 value2` or `Type WITH field IS value` |
| Field access          | `record's field`                                   |
| Pattern match         | `CONSIDER expr WHEN pattern THEN result`           |
| Check list membership | `elem item list`                                   |

---

Next, [Module 3: Control flow](/l4/courses/foundation/module-3-control-flow.md) covers conditional logic, lists and boolean operators.
