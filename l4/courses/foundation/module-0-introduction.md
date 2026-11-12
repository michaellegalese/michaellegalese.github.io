# Module 0: Introduction to L4

This module introduces the language and gets you set up.

## Learning Objectives

By the end of this module, you will be able to:

- Explain what L4 is and what it is designed for
- Recognize the main building blocks of an L4 file
- Set up an L4 environment (VS Code, web editor, or command line)
- Create and run your first L4 file

---

## What is L4?

**L4 is a programming language designed specifically for law.** It lets you translate legal documents into precise, executable code that computers can understand, verify, and run.

### Why L4?

Traditional legal documents have problems:

| Problem           | Example                          | L4 Solution                     |
| ----------------- | -------------------------------- | ------------------------------- |
| **Ambiguity**     | "reasonable time"                | Explicit deadlines: `WITHIN 30` |
| **Inconsistency** | Same term defined differently    | Single source of truth          |
| **Complexity**    | Nested conditions hard to follow | Visual logic diagrams           |
| **Testing**       | Can't simulate scenarios         | `#TRACE` runs simulations       |
| **Maintenance**   | Changes ripple unpredictably     | Type system catches errors      |

### What L4 Is For

L4 excels at:

- **Regulatory compliance** - Encode legislation as executable rules
- **Contract automation** - Model obligations, deadlines, and consequences
- **Decision support** - Build systems that explain their reasoning
- **Legal analysis** - Find edge cases and contradictions

### What L4 Is Not

L4 is not:

- A replacement for lawyers (it's a tool for lawyers)
- Natural language processing (you write structured code)
- A document generator (though it can feed into one)

---

## How L4 Works

L4 code looks like a hybrid of legal text and programming. Here's a complete example of a charity's legal obligation:

**Example:** 

```l4-file
-- Example: A charity's obligation to file annual returns
-- From Module 0: Introduction to L4

-- First, define what a charity looks like
DECLARE `Charity Status` IS ONE OF
    Active
    Suspended
    Dissolved

DECLARE `Registered Charity`
    HAS `the name` IS A STRING
        `the registration number` IS A STRING
        `the status` IS A `Charity Status`

-- Define the actors and actions for our obligation
DECLARE Party IS ONE OF
    `the charity`
    `the Commissioner`

DECLARE Action IS ONE OF
    `file the annual return`
    `issue a Required Steps Notice`

-- The legal obligation: active charities must file annual returns
GIVEN charity IS A `Registered Charity`
GIVETH A DEONTIC Party Action
`the charity must file its annual return` MEANS
    IF charity's `the status` EQUALS Active
    THEN PARTY `the charity`
         MUST `file the annual return`
         WITHIN 60
         HENCE FULFILLED
         LEST PARTY `the Commissioner`
              MAY `issue a Required Steps Notice`
              HENCE FULFILLED
    ELSE FULFILLED

-- Create a test charity
`Acme Animal Shelter` MEANS `Registered Charity` "Acme Animal Shelter" "JC-2024-001" Active

-- Test the obligation
#TRACE `the charity must file its annual return` `Acme Animal Shelter` AT 0 WITH
    PARTY `the charity` DOES `file the annual return` AT 30
```



This may be a lot right now, but notice how it reads almost like English, but with precise meaning:

- **DECLARE** - Defines what things look like (types)
- **GIVEN** - Declares what entities are involved
- **IF** - States conditions that must be true
- **PARTY** - Identifies who has the obligation
- **MUST** - Creates a legal obligation
- **WITHIN** - Sets a deadline (in days)
- **HENCE** - What happens on compliance
- **LEST** - What happens on non-compliance
- **#TRACE** - Simulates how the contract plays out

---

## Key Concepts

### 1. Types Define Structure

L4 uses types to define what things look like:

**Example:** 

```l4-file
-- Example: Defining types in L4
-- From Module 0: Introduction to L4

-- A simple record type describing a registered charity
DECLARE `Registered Charity`
    HAS `the name` IS A STRING
        `the registration number` IS A STRING
        `the annual income` IS A NUMBER

-- Create an example charity using positional arguments
`Example Charity` MEANS `Registered Charity` "Jersey Wildlife Trust" "JC-2024-042" 75000

-- Access its fields
#EVAL `Example Charity`'s `the name`
#EVAL `Example Charity`'s `the annual income`
```



This prevents errors—you can't accidentally use a person where you need a charity.

### 2. Rules Define Logic

Rules capture legal reasoning using natural language:

**Example:** 

```l4-file
-- Example: A simple eligibility rule
-- From Module 0: Introduction to L4

-- Define what an applicant looks like
DECLARE Applicant
    HAS `the applicant's name` IS A STRING
        `the applicant's age` IS A NUMBER
        `the applicant has valid ID` IS A BOOLEAN

-- Define the eligibility rule in natural language
GIVEN applicant IS A Applicant
GIVETH A BOOLEAN
DECIDE `the applicant is eligible` IF
    `the applicant is at least 18 years old` applicant
    AND `the applicant has shown valid identification` applicant

-- Break down the conditions into readable sub-rules
GIVEN applicant IS A Applicant
GIVETH A BOOLEAN
`the applicant is at least 18 years old` MEANS
    applicant's `the applicant's age` >= 18

GIVEN applicant IS A Applicant
GIVETH A BOOLEAN
`the applicant has shown valid identification` MEANS
    applicant's `the applicant has valid ID` EQUALS TRUE

-- Create test applicants
`Alice the adult` MEANS Applicant "Alice" 25 TRUE
`Bob without ID` MEANS Applicant "Bob" 30 FALSE
`Young Charlie` MEANS Applicant "Charlie" 16 TRUE

-- Test the rule
#EVAL `the applicant is eligible` `Alice the adult`
#EVAL `the applicant is eligible` `Bob without ID`
#EVAL `the applicant is eligible` `Young Charlie`
```



Notice how the rule reads like English: "the applicant is eligible if the applicant is at least 18 years old and the applicant has shown valid identification."

### 3. Contracts Define Obligations

Regulative rules capture who must do what, with deadlines and consequences:

**Example:** 

```l4-file
-- Example: A simple sale contract with chained obligations
-- From Module 0: Introduction to L4

-- Define who can be a party to the contract
DECLARE Party IS ONE OF
    `the seller`
    `the buyer`

-- Define what actions can be performed
DECLARE `Contract Action` IS ONE OF
    `deliver the goods`
    `pay the invoice`
    `cancel the order`

-- The sale contract: seller delivers, then buyer pays
GIVETH A DEONTIC Party `Contract Action`
`the sale contract` MEANS
    PARTY `the seller`
    MUST `deliver the goods`
    WITHIN 14
    HENCE
        PARTY `the buyer`
        MUST `pay the invoice`
        WITHIN 30
        HENCE FULFILLED
        LEST BREACH
    LEST
        PARTY `the seller`
        MAY `cancel the order`
        HENCE FULFILLED

-- Simulate: seller delivers on day 10, buyer pays on day 35
#TRACE `the sale contract` AT 0 WITH
    PARTY `the seller` DOES `deliver the goods` AT 10
    PARTY `the buyer` DOES `pay the invoice` AT 35
```



This models a simple sale: the seller must deliver within 14 days, then the buyer must pay within 30 days.

---

## Setting Up Your Environment

You can write L4 in three places:

- **VS Code** (recommended): install the [L4 extension](https://marketplace.visualstudio.com/items?itemName=Legalese.l4-vscode) and create a file ending in `.l4`. You get syntax highlighting, errors as you type, hover documentation and visualisations.
- **The web editor**: [play.legalese.com](https://play.legalese.com/) runs L4 in your browser, with nothing to install.
- **The command line**: `l4 run myfile.l4` checks a file and prints its results.

[Install L4 and write your first rule](/l4/tutorials/getting-started/first-l4-file.md) walks through each option step by step.

---

## Your First L4 File

Create a file called `hello.l4`:



```l4-file
-- This is a comment (starts with --)

-- Define a simple type
DECLARE Person
    HAS name IS A STRING
        age IS A NUMBER

-- Create a person
alice MEANS Person "Alice" 30

-- Define a rule
GIVEN p IS A Person
GIVETH A BOOLEAN
DECIDE `is adult` IF p's age >= 18

-- Test it
#EVAL `is adult` alice
```



Save the file. If you're using VS Code with the L4 extension, you'll see:

- Syntax highlighting
- The `#EVAL` result shown inline

---

## L4 Design Philosophy

L4 is built so that code mirrors the structure of the legal text it encodes, can be read by lawyers, and has one precise, checkable meaning. [Design principles](/l4/concepts/language-design/principles.md) explains these choices and the trade-offs between them.

Next, [Module 1: Your first legal rule](/l4/courses/foundation/module-1-first-rule.md) writes a complete legal obligation with a condition, a deadline and consequences.
