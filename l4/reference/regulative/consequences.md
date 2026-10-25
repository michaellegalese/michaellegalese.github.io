# Deadlines and consequences

A regulative rule says who must, may or must not act (see [PARTY, MUST, MAY, SHANT](/l4/reference/regulative/PARTY.md)). This page covers the rest of the rule: the deadline (`WITHIN`), what follows (`HENCE`, `LEST`), how the action is matched (`PROVIDED`, `EXACTLY`), the end states (`FULFILLED`, `BREACH`), and rules that run side by side (`RAND`, `ROR`).

```text
PARTY party
MUST action PROVIDED condition
WITHIN deadline
HENCE rule-if-complied
LEST rule-if-not
```

**Example file:** 

```l4-file
-- DEONTIC Type Examples - Regulative Values in L4

-- ============================================================
-- § Type Declarations
-- ============================================================

-- Every DEONTIC value needs Actor and Action types
-- These define WHO can act and WHAT actions exist

DECLARE Actor IS ONE OF
    Buyer
    Seller
    Commissioner

DECLARE Action IS ONE OF
    `deliver goods` HAS description IS A STRING
    `pay invoice` HAS amount IS A NUMBER
    `inspect goods`
    `issue notice` HAS content IS A STRING
    `cancel order`

-- ============================================================
-- § Basic DEONTIC Values
-- ============================================================

-- A DEONTIC value represents an obligation, permission, or prohibition
-- The type signature shows the Actor and Action types

GIVETH A DEONTIC Actor Action
`simple delivery obligation` MEANS
    PARTY Seller
    MUST `deliver goods` "standard package"
    WITHIN 14
    HENCE FULFILLED
    LEST BREACH

-- DEONTIC values are first-class - they can be stored in variables
GIVETH A DEONTIC Actor Action
`the seller's duty` MEANS `simple delivery obligation`

-- ============================================================
-- § DEONTIC with Parameters
-- ============================================================

-- Functions can take parameters and return DEONTIC values

GIVEN deadline IS A NUMBER
GIVETH A DEONTIC Actor Action
`delivery within days` MEANS
    PARTY Seller
    MUST `deliver goods` "standard package"
    WITHIN deadline
    HENCE FULFILLED
    LEST BREACH

GIVEN price IS A NUMBER
      `payment deadline` IS A NUMBER
GIVETH A DEONTIC Actor Action
`payment obligation` MEANS
    PARTY Buyer
    MUST `pay invoice` (EXACTLY price)
    WITHIN `payment deadline`
    HENCE FULFILLED
    LEST BREACH

-- ============================================================
-- § DEONTIC Composition with HENCE
-- ============================================================

-- HENCE chains DEONTIC values - one obligation leads to another

GIVEN price IS A NUMBER
GIVETH A DEONTIC Actor Action
`sale contract` MEANS
    PARTY Seller
    MUST `deliver goods` "merchandise"
    WITHIN 14
    HENCE
        PARTY Buyer
        MUST `pay invoice` (EXACTLY price)
        WITHIN 30
        HENCE FULFILLED
        LEST BREACH BY Buyer BECAUSE "failed to pay"
    LEST BREACH BY Seller BECAUSE "failed to deliver"

-- ============================================================
-- § DEONTIC Composition with RAND (Parallel)
-- ============================================================

-- RAND combines DEONTIC values that must ALL be fulfilled

GIVETH A DEONTIC Actor Action
`mutual obligations` MEANS
    (PARTY Seller MUST `deliver goods` "item A" WITHIN 14 HENCE FULFILLED LEST BREACH)
    RAND
    (PARTY Buyer MUST `pay invoice` 1000 WITHIN 30 HENCE FULFILLED LEST BREACH)

-- ============================================================
-- § DEONTIC Composition with ROR (Alternative)
-- ============================================================

-- ROR combines DEONTIC values where ANY ONE fulfills the contract
-- Note: ROR applies to entire provisions, not individual obligations

GIVETH A DEONTIC Actor Action
`flexible delivery` MEANS
    (PARTY Seller MUST `deliver goods` "express shipping" WITHIN 7 HENCE FULFILLED LEST BREACH)
    ROR
    (PARTY Seller MUST `deliver goods` "standard shipping" WITHIN 14 HENCE FULFILLED LEST BREACH)

-- ============================================================
-- § Terminal DEONTIC Values
-- ============================================================

-- FULFILLED - The contract is successfully completed
-- BREACH - The contract is broken (optionally with blame)

GIVETH A DEONTIC Actor Action
`already fulfilled` MEANS FULFILLED

GIVETH A DEONTIC Actor Action
`immediate breach` MEANS BREACH

GIVETH A DEONTIC Actor Action
`breach with blame` MEANS BREACH BY Seller BECAUSE "goods were defective"

-- ============================================================
-- § Conditional DEONTIC Values
-- ============================================================

-- DEONTIC values can be computed conditionally

GIVEN `is urgent` IS A BOOLEAN
GIVETH A DEONTIC Actor Action
`conditional delivery` MEANS
    IF `is urgent`
    THEN
        PARTY Seller
        MUST `deliver goods` "urgent delivery"
        WITHIN 3
        HENCE FULFILLED
        LEST BREACH
    ELSE
        PARTY Seller
        MUST `deliver goods` "standard delivery"
        WITHIN 14
        HENCE FULFILLED
        LEST BREACH

-- ============================================================
-- § The EVALTRACE Function
-- ============================================================

-- EVALTRACE is the underlying function for evaluating contracts
-- Type: FORALL party action. DEONTIC party action -> NUMBER -> LIST (EVENT party action) -> DEONTIC party action

-- Create events using the EVENT constructor
`test events` MEANS LIST EVENT Seller (`deliver goods` "standard package") 5, EVENT Buyer (`pay invoice` 100) 20

-- Evaluate a contract against events starting at time 0
#EVAL EVALTRACE `simple delivery obligation` 0 `test events`

-- ============================================================
-- § Testing DEONTIC Values with #TRACE
-- ============================================================

-- #TRACE is syntactic sugar for EVALTRACE

-- Test 1: Simple obligation fulfilled
#TRACE `simple delivery obligation` AT 0 WITH
    PARTY Seller DOES `deliver goods` "standard package" AT 10

-- Test 2: Simple obligation breached (deadline passed)
#TRACE `simple delivery obligation` AT 0 WITH
    PARTY Seller DOES `deliver goods` "standard package" AT 20

-- Test 3: Parameterized delivery
#TRACE `delivery within days` 7 AT 0 WITH
    PARTY Seller DOES `deliver goods` "standard package" AT 5

-- Test 4: Sale contract - happy path
#TRACE `sale contract` 500 AT 0 WITH
    PARTY Seller DOES `deliver goods` "merchandise" AT 10
    PARTY Buyer DOES `pay invoice` 500 AT 30

-- Test 5: Mutual obligations with RAND
#TRACE `mutual obligations` AT 0 WITH
    PARTY Seller DOES `deliver goods` "item A" AT 10
    PARTY Buyer DOES `pay invoice` 1000 AT 25

-- Test 6: Flexible delivery with ROR (first option)
#TRACE `flexible delivery` AT 0 WITH
    PARTY Seller DOES `deliver goods` "express shipping" AT 5

-- Test 7: Flexible delivery with ROR (second option)
#TRACE `flexible delivery` AT 0 WITH
    PARTY Seller DOES `deliver goods` "standard shipping" AT 12

-- Test 8: Conditional delivery (urgent)
#TRACE `conditional delivery` TRUE AT 0 WITH
    PARTY Seller DOES `deliver goods` "urgent delivery" AT 2

-- Test 9: Conditional delivery (not urgent)
#TRACE `conditional delivery` FALSE AT 0 WITH
    PARTY Seller DOES `deliver goods` "standard delivery" AT 10

-- ============================================================
-- § Advanced: PROVIDED Clause for Pattern Matching
-- ============================================================

-- PROVIDED adds conditions to action pattern matching

GIVETH A DEONTIC Actor Action
`minimum payment contract` MEANS
    PARTY Buyer
    MUST `pay invoice` amount PROVIDED amount >= 100
    WITHIN 30
    HENCE FULFILLED
    LEST BREACH

-- Test: Payment meets minimum
#TRACE `minimum payment contract` AT 0 WITH
    PARTY Buyer DOES `pay invoice` 150 AT 20

-- Test: Payment below minimum (doesn't match, continues waiting)
#TRACE `minimum payment contract` AT 0 WITH
    PARTY Buyer DOES `pay invoice` 50 AT 20

-- ============================================================
-- § Advanced: EXACTLY for Precise Matching
-- ============================================================

-- EXACTLY requires an exact value match in patterns

GIVEN `required amount` IS A NUMBER
GIVETH A DEONTIC Actor Action
`exact payment required` MEANS
    PARTY Buyer
    MUST EXACTLY `pay invoice` `required amount`
    WITHIN 30
    HENCE FULFILLED
    LEST BREACH

-- Test: Exact amount paid
#TRACE `exact payment required` 500 AT 0 WITH
    PARTY Buyer DOES `pay invoice` 500 AT 15

-- Test: Wrong amount (doesn't match)
#TRACE `exact payment required` 500 AT 0 WITH
    PARTY Buyer DOES `pay invoice` 499 AT 15

-- ============================================================
-- § Advanced: WAITUNTIL for Time Advancement
-- ============================================================

-- WAITUNTIL advances time without an event
-- Useful for testing deadline behavior

#TRACE `simple delivery obligation` AT 0 WITH
    WAITUNTIL 20

-- ============================================================
-- § DEONTIC as Return Values
-- ============================================================

-- Functions can compute and return DEONTIC values dynamically

GIVEN `order type` IS A STRING
      price IS A NUMBER
GIVETH A DEONTIC Actor Action
`create order contract` MEANS
    IF `order type` EQUALS "express"
    THEN
        PARTY Seller
        MUST `deliver goods` "express order"
        WITHIN 3
        HENCE
            PARTY Buyer
            MUST `pay invoice` (EXACTLY `express price`)
            WITHIN 7
            HENCE FULFILLED
            LEST BREACH
        LEST BREACH
    ELSE
        PARTY Seller
        MUST `deliver goods` "standard order"
        WITHIN 14
        HENCE
            PARTY Buyer
            MUST `pay invoice` (EXACTLY price)
            WITHIN 30
            HENCE FULFILLED
            LEST BREACH
        LEST BREACH
    WHERE
        `express price` MEANS price + 50

-- Test express order
#TRACE `create order contract` "express" 200 AT 0 WITH
    PARTY Seller DOES `deliver goods` "express order" AT 2
    PARTY Buyer DOES `pay invoice` 250 AT 5

-- Test standard order
#TRACE `create order contract` "standard" 200 AT 0 WITH
    PARTY Seller DOES `deliver goods` "standard order" AT 10
    PARTY Buyer DOES `pay invoice` 200 AT 30
```



## WITHIN

`WITHIN` gives the time the party has to act, as a NUMBER on the same timeline as the events in a `#TRACE`.
The timeline has no fixed unit: days, hours or business days all work, as long as the deadlines and the event times use the same one.

```l4
DECLARE Party IS ONE OF Buyer, Seller
DECLARE Action IS ONE OF `confirm order`, deliver

GIVETH A DEONTIC Party Action
`order and delivery` MEANS
  PARTY Buyer
  MUST `confirm order`
  WITHIN 30
  HENCE
    PARTY Seller
    MUST deliver
    WITHIN 5
    HENCE FULFILLED
    LEST BREACH BY Seller BECAUSE "late delivery"

#TRACE `order and delivery` AT 0 WITH
  PARTY Buyer DOES `confirm order` AT 10
  PARTY Seller DOES deliver AT 14
```

- A deadline counts from the moment its rule becomes active: from the start time for the first rule, and from the event that led to it for a rule after `HENCE` or `LEST`. Above, the buyer confirms at 10, so the seller must deliver by 15.
- Deadlines are relative. There is no absolute date such as "by 1 January 2026", and no `WITHIN 5 days OF event`: to count from an event, put the rule after the `HENCE` of the rule for that event, as above.
- As events are read, the time left shrinks: after an event at 12, what was `WITHIN 14` from 0 has 2 left.
- A rule without `WITHIN` has no deadline. It waits for its action however long it takes, and time alone never takes it to `LEST`.
- L4 finds that a deadline has passed when it reads an event, or a `WAITUNTIL` (see [EVENT](/l4/reference/regulative/EVENT.md)), with a later time. The rule that follows starts at that event's time, and that event is not matched against it.

## HENCE and LEST

`HENCE` names what follows when the rule is complied with, and `LEST` what follows when it is not.
Each can be `FULFILLED`, `BREACH`, or another rule, so rules chain into a contract.
A `LEST` that is another rule expresses a remedy, such as a penalty or a grace period:

```l4
DECLARE Party IS ONE OF Borrower
DECLARE Action IS ONE OF pay HAS amount IS A NUMBER

GIVETH A DEONTIC Party Action
`payment with penalty` MEANS
  PARTY Borrower
  MUST pay 1000
  WITHIN 30
  HENCE FULFILLED
  LEST
    PARTY Borrower
    MUST pay 1100
    WITHIN 15
    LEST BREACH
```

Which branch is taken, and what is used when it is left out, depends on the modal:

| Modal              | `HENCE` is taken when                                          | `LEST` is taken when           | `HENCE` default | `LEST` default |
| ------------------ | -------------------------------------------------------------- | ------------------------------ | --------------- | -------------- |
| `MUST`             | the action is taken in time                                    | the deadline passes without it | `FULFILLED`     | `BREACH`       |
| `MAY`              | the action is taken in time, or the deadline passes without it | never                          | `FULFILLED`     | not used       |
| `SHANT / MUST NOT` | the deadline passes without the action                         | the action is taken            | `FULFILLED`     | `BREACH`       |
| `DO`               | the action is taken in time                                    | the deadline passes without it | `FULFILLED`     | `BREACH`       |

For a prohibition the polarity flips: the deadline passing quietly is the good outcome. `HENCE` always names the outcome the rule wants, and `LEST` the one it does not.

A rule written on the same line as its `HENCE` needs parentheses when a `LEST` follows on the next line: `HENCE (PARTY Seller MUST deliver WITHIN 5)`. Without them, that `LEST` is an indentation error. A rule on lines of its own, as above, needs none.

## FULFILLED and BREACH

`FULFILLED` and `BREACH` are the two end states. `FULFILLED` means nothing more is owed; `BREACH` means a party failed and nothing absorbed the failure.

```text
LEST BREACH
LEST BREACH BY party
LEST BREACH BECAUSE reason
LEST BREACH BY party BECAUSE reason
```

`BY` names the party in breach and [BECAUSE](/l4/reference/regulative/BECAUSE.md) gives the reason, a STRING.
`FULFILLED` is also the natural result for a case where no duty arises: ``IF NOT `conditions are met` THEN FULFILLED ELSE PARTY …``.

## PROVIDED

`PROVIDED` adds a condition to the action. An event first has to match the action; then the condition is evaluated, with the names the action bound. If it is FALSE, the event does not count and the next one is tried.

```l4
DECLARE Party IS ONE OF Buyer
DECLARE Action IS ONE OF `pay invoice` HAS amount IS A NUMBER

GIVETH A DEONTIC Party Action
`minimum payment` MEANS
  PARTY Buyer
  MUST `pay invoice` amount PROVIDED amount >= 100
  WITHIN 30

#TRACE `minimum payment` AT 0 WITH
  PARTY Buyer DOES `pay invoice` 50 AT 5
  PARTY Buyer DOES `pay invoice` 150 AT 6
```

The payment of 50 does not count; the payment of 150 fulfils the rule. Without `PROVIDED`, the condition is TRUE.

## EXACTLY

The action after the modal is a pattern, as in a [CONSIDER](/l4/reference/control-flow/CONSIDER.md):

- a value, such as `pay 1000` or `deliver "goods"`, matches only that value;
- a name, such as `pay amount`, matches anything and binds the name for `PROVIDED` and for the rules after `HENCE` and `LEST`. This holds even when a name with that spelling is already defined: with `GIVEN price IS A NUMBER`, `MUST pay price` matches a payment of any amount.

`EXACTLY` turns an expression into a value to match. Use it to compare with an input or another defined value:

```l4
DECLARE Party IS ONE OF Buyer
DECLARE Action IS ONE OF payment HAS amount IS A NUMBER

GIVEN price IS A NUMBER
GIVETH A DEONTIC Party Action
`pay the price` price MEANS
  PARTY Buyer
  MUST payment (EXACTLY price)
  WITHIN 10
```

`EXACTLY` can also come before the whole action: `MUST EXACTLY payment price`.

## RAND and ROR

`RAND` and `ROR` combine two rules that run at the same time, on the same events.

- `A RAND B` is fulfilled when both are fulfilled, and breached as soon as either is breached.
- `A ROR B` is fulfilled as soon as either is fulfilled, and breached only when both are breached.

```l4
DECLARE Party IS ONE OF Seller
DECLARE Action IS ONE OF ship, `arrange pickup`

GIVETH A DEONTIC Party Action
`either way of delivering` MEANS
     (PARTY Seller MUST ship WITHIN 14 HENCE FULFILLED LEST BREACH)
  ROR
     (PARTY Seller MUST `arrange pickup` WITHIN 7 HENCE FULFILLED LEST BREACH)
```

`RAND` binds more tightly than `ROR`: `A ROR B RAND C` means `A ROR (B RAND C)`.
A combination can follow `HENCE` or `LEST`, so one event can start several duties.

## Related pages

- [Regulative rules](/l4/reference/regulative.md): the parts of a rule and `#TRACE`
- [DEONTIC](/l4/reference/regulative/DEONTIC.md): the type of rules, and how they are evaluated
- [How obligations unfold](/l4/concepts/legal-modeling/regulative-rules.md): the model behind deadlines and events
