# DEONTIC

`DEONTIC` is the type of regulative rules: obligations, permissions and prohibitions.
It takes two type parameters, the type of the parties and the type of the actions.

## Syntax

```text
GIVETH A DEONTIC PartyType ActionType
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



## Example

```l4
DECLARE Actor IS ONE OF Buyer, Seller
DECLARE Action IS ONE OF
  `deliver goods`
  `pay invoice` HAS amount IS A NUMBER

GIVETH A DEONTIC Actor Action
`delivery obligation` MEANS
    PARTY Seller
    MUST `deliver goods`
    WITHIN 14
    HENCE FULFILLED
    LEST BREACH
```

Every `PARTY` in the rule must be an `Actor`, and every action an `Action`.

## Values

A value of type `DEONTIC Party Action` is one of:

- a rule, `PARTY … MUST/MAY/SHANT …` (see [PARTY, MUST, MAY, SHANT](/l4/reference/regulative/PARTY.md));
- `FULFILLED`: nothing more is owed;
- `BREACH`, optionally `BREACH BY party BECAUSE reason`: a party failed;
- two rules combined with `RAND` or `ROR` (see [Deadlines and consequences](/l4/reference/regulative/consequences.md#rand-and-ror)).

Like any other value, a `DEONTIC` value can be the result of a function with `GIVEN` inputs, or of an `IF` or a `CONSIDER`:

```l4
DECLARE Actor IS ONE OF Buyer, Seller
DECLARE Action IS ONE OF `deliver goods`

GIVEN `is urgent` IS A BOOLEAN
GIVETH A DEONTIC Actor Action
`delivery` `is urgent` MEANS
    IF `is urgent`
    THEN PARTY Seller MUST `deliver goods` WITHIN 3
    ELSE PARTY Seller MUST `deliver goods` WITHIN 14
```

## Evaluation

A rule decides nothing on its own. It is evaluated from a start time against a list of events, each saying which party did what, and when (see [EVENT](/l4/reference/regulative/EVENT.md)).
The result is a `DEONTIC` value again:

- `FULFILLED`, when every duty was discharged;
- `BREACH`, with who missed which deadline, when a duty failed and nothing absorbed the failure;
- the rule still waiting, when the events run out before either happens. This value can be given more events later.

`#TRACE` evaluates a rule in a file:

```text
#TRACE rule AT start-time WITH
  PARTY party DOES action AT time
  ...
```

`EVALTRACE` is the same evaluation as a function:

```text
EVALTRACE : DEONTIC party action → NUMBER → LIST OF EVENT party action → DEONTIC party action
```

```l4
DECLARE Actor IS ONE OF Buyer, Seller
DECLARE Action IS ONE OF `deliver goods`

GIVETH A DEONTIC Actor Action
`delivery obligation` MEANS
    PARTY Seller
    MUST `deliver goods`
    WITHIN 14

#TRACE `delivery obligation` AT 0 WITH
    PARTY Seller DOES `deliver goods` AT 10

#EVAL EVALTRACE `delivery obligation` 0 (LIST EVENT Seller `deliver goods` 10)
```

Both give `FULFILLED`. With no events at all, both give the rule still waiting, not `BREACH`: to let a deadline pass without anyone acting, add a `WAITUNTIL` event.

`l4 state-graph` draws a rule's possible paths as a state diagram; see [Using the l4 CLI](/l4/tutorials/getting-started/l4-cli.md).

## Related pages

- [Regulative rules](/l4/reference/regulative.md)
- [PARTY, MUST, MAY, SHANT](/l4/reference/regulative/PARTY.md)
- [Deadlines and consequences](/l4/reference/regulative/consequences.md)
- [EVENT](/l4/reference/regulative/EVENT.md)
- [Foundation module 6: Regulative rules](/l4/courses/foundation/module-6-regulative.md): a step-by-step introduction
- [How obligations unfold](/l4/concepts/legal-modeling/regulative-rules.md): the model behind regulative rules
- [Tom Hvitved's PhD thesis](https://di.ku.dk/english/research/phd/phd-theses/2011/hvitved12phd.pdf): the theory L4's contract model is based on
