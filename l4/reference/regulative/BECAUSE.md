# BECAUSE

`BECAUSE` gives the reason for a breach. It comes after `BREACH`, optionally with `BY` and the party in breach.

## Syntax

```text
BREACH BECAUSE reason
BREACH BY party BECAUSE reason
```

| Form                                | Records                |
| ----------------------------------- | ---------------------- |
| `BREACH`                            | that a breach happened |
| `BREACH BY Seller`                  | who is in breach       |
| `BREACH BECAUSE "reason"`           | why                    |
| `BREACH BY Seller BECAUSE "reason"` | who and why            |

The reason is a STRING, either text in quotes or an expression that gives text.

**Example file:** 

```l4-file
-- BECAUSE Keyword Examples
-- Demonstrates breach reasons in regulative rules

--------------------------------------------------------------------------------
-- Type Declarations
--------------------------------------------------------------------------------

DECLARE Person IS ONE OF Seller, Buyer, Employee, Employer, Tenant, Landlord

DECLARE Action IS ONE OF
  `deliver goods`
  `pay amount` HAS amount IS A NUMBER
  `pass inspection`
  `disclose information`
  `pay rent` HAS amount IS A NUMBER
  `maintain property`
  smoke

--------------------------------------------------------------------------------
-- Basic BECAUSE Usage
--------------------------------------------------------------------------------

-- Simple breach with reason only
`delivery with reason` MEANS
  PARTY Seller
  MUST `deliver goods`
  WITHIN 14
  HENCE FULFILLED
  LEST BREACH BECAUSE "delivery deadline exceeded"

-- Full form: BY party BECAUSE reason
`delivery full form` MEANS
  PARTY Seller
  MUST `deliver goods`
  WITHIN 14
  HENCE FULFILLED
  LEST BREACH BY Seller BECAUSE "failed to deliver within contractual period"

--------------------------------------------------------------------------------
-- Distinguishing Different Breach Types
--------------------------------------------------------------------------------

-- Payment deadline breach
`payment deadline` MEANS
  PARTY Buyer
  MUST `pay amount` 1000
  WITHIN 30
  HENCE FULFILLED
  LEST BREACH BY Buyer BECAUSE "payment received after deadline"

-- Quality inspection failure
`quality check` MEANS
  PARTY Seller
  MUST `pass inspection`
  WITHIN 7
  HENCE FULFILLED
  LEST BREACH BY Seller BECAUSE "goods failed quality inspection per spec A.3"

--------------------------------------------------------------------------------
-- Referencing Legal Text in Reasons
--------------------------------------------------------------------------------

-- Reference to contract section
`nda obligation` MEANS
  PARTY Employee
  SHANT `disclose information`
  HENCE FULFILLED
  LEST BREACH BY Employee BECAUSE "violated NDA section 3.2"

-- Reference to lease agreement
`subletting prohibition` MEANS
  PARTY Tenant
  SHANT `disclose information`
  HENCE FULFILLED
  LEST BREACH BY Tenant BECAUSE "violated lease agreement section 8"

--------------------------------------------------------------------------------
-- Chained Obligations with Different Reasons
--------------------------------------------------------------------------------

`rental agreement` MEANS
  PARTY Tenant
  MUST `pay rent` 2000
  WITHIN 5
  HENCE (
    PARTY Landlord
    MUST `maintain property`
    WITHIN 30
    HENCE FULFILLED
    LEST BREACH BY Landlord BECAUSE "failed to perform maintenance duties"
  )
  LEST BREACH BY Tenant BECAUSE "rent payment overdue"

--------------------------------------------------------------------------------
-- BECAUSE in Prohibition (SHANT)
--------------------------------------------------------------------------------

`no smoking policy` MEANS
  PARTY Employee
  SHANT smoke
  HENCE FULFILLED
  LEST BREACH BY Employee BECAUSE "violated workplace no-smoking policy"

--------------------------------------------------------------------------------
-- Testing with #TRACE
--------------------------------------------------------------------------------

-- Test 1: Obligation fulfilled (no breach)
#TRACE `delivery with reason` AT 0 WITH
  PARTY Seller DOES `deliver goods` AT 10

-- Test 2: Obligation breached (deadline passes without action)
-- This will show the BECAUSE reason in the breach output
#TRACE `delivery full form` AT 0 WITH
  WAITUNTIL 15

-- Test 3: Prohibition violated
#TRACE `no smoking policy` AT 0 WITH
  PARTY Employee DOES smoke AT 5

-- Test 4: Chained obligation - first party fulfills, second breaches
#TRACE `rental agreement` AT 0 WITH
  PARTY Tenant DOES `pay rent` 2000 AT 3
  WAITUNTIL 40
```



## Example

```l4
DECLARE Party IS ONE OF Buyer, Seller
DECLARE Action IS ONE OF `pay invoice` HAS amount IS A NUMBER

`the amount due` MEANS 1000

GIVETH A DEONTIC Party Action
`payment` MEANS
  PARTY Buyer
  MUST `pay invoice` (EXACTLY `the amount due`)
  WITHIN 30
  HENCE FULFILLED
  LEST BREACH BY Buyer BECAUSE (CONCAT "failed to pay ", TOSTRING `the amount due`)

#TRACE `payment` AT 0 WITH
  WAITUNTIL 31
```

The `#TRACE` result shows the breach with its party and reason: `BREACH BY Buyer BECAUSE "failed to pay 1000"`.

`BREACH … BECAUSE` usually follows `LEST`, but it can stand wherever a rule can, for example after `HENCE` or in a branch of an `IF`.
When a contract has several ways to fail, give each its own reason, and cite the clause or section it comes from, so a reader of the result knows which duty failed and why.

## Related pages

- [Deadlines and consequences: FULFILLED and BREACH](/l4/reference/regulative/consequences.md#fulfilled-and-breach)
- [PARTY, MUST, MAY, SHANT](/l4/reference/regulative/PARTY.md)
- [DEONTIC](/l4/reference/regulative/DEONTIC.md)
