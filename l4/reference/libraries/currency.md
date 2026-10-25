# currency

Currency handling with ISO 4217 currency codes. Stores amounts as integer minor units (cents) to avoid floating-point errors. Import with `IMPORT currency` (also imports jurisdiction).

**Example:** 

```l4-file
-- Currency Example: ISO 4217 currency handling
IMPORT currency

-- Currency codes
#EVAL `US Dollar code`  -- "USD"
#EVAL `Euro code`       -- "EUR"

-- Decimal places per currency
#EVAL `decimal places for currency` "USD"  -- RIGHT 2
#EVAL `decimal places for currency` "JPY"  -- RIGHT 0 (no decimals)
#EVAL `decimal places for currency` "XXX"  -- LEFT "Unknown currency code: XXX"

-- Create money from minor units (cents)
-- 12345 cents = $123.45
DECIDE priceInCents IS 12345
DECIDE formattedPrice IS Money priceInCents "USD"
#EVAL formattedPrice  -- RIGHT "123.45 USD"

-- Japanese Yen has no decimals
DECIDE yenAmount IS Money 1000 "JPY"
#EVAL yenAmount  -- RIGHT "1000 JPY"

-- Convert between major and minor units
DECIDE dollars IS 99.99
DECIDE cents IS `major to minor units` dollars "USD"
#EVAL cents  -- RIGHT 9999

DECIDE backToDollars IS `minor to major units` 9999 "USD"
#EVAL backToDollars  -- RIGHT 99.99

-- Arithmetic (all in minor units to avoid floating point errors)
DECIDE amount1 IS 1000  -- $10.00
DECIDE amount2 IS 500   -- $5.00

DECIDE added IS `add money` amount1 amount2 "USD"
#EVAL added  -- RIGHT 1500 ($15.00)

DECIDE subtracted IS `subtract money` amount1 amount2 "USD"
#EVAL subtracted  -- RIGHT 500 ($5.00)

-- Multiply by factor (e.g., tax)
DECIDE taxRate IS 1.08  -- 8% tax
DECIDE withTax IS `multiply money` amount1 taxRate "USD"
#EVAL withTax  -- RIGHT 1080 ($10.80)

-- Comparisons
#EVAL `money greater than` 1000 500  -- TRUE
#EVAL `money at least` 1000 1000     -- TRUE

-- Validation
#EVAL `is valid currency code` "USD"  -- TRUE
#EVAL `is valid currency code` "usd"  -- FALSE (must be uppercase)
#EVAL `is non-negative` 100           -- TRUE
#EVAL `is positive` 0                 -- FALSE
```



## Supported currencies

USD, EUR, GBP, JPY, CHF, CAD, AUD, CNY, HKD, SGD, INR, BRL, MXN, SEK, NZD, KRW

Each currency provides constants, e.g. `` `US Dollar code` `` (`"USD"`), `` `US Dollar numeric` `` (840), `` `US Dollar decimals` `` (2), and `` `US Dollar country` `` (the euro has `` `Euro region` `` instead of a country). JPY and KRW have 0 decimal places; all others 2.

## Construction and formatting

| Function                                   | Signature                                | Description                                                                                                                                      |
| ------------------------------------------ | ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Money` minorUnits currencyCode            | `NUMBER → STRING → EITHER STRING STRING` | Format minor units as an amount string, e.g. `Money 12345 "USD"` → `RIGHT "123.45 USD"`. LEFT for unknown currencies or non-integer minor units. |
| `decimal places for currency` currencyCode | `STRING → EITHER STRING NUMBER`          | Minor-unit decimal places for a currency code                                                                                                    |
| `power of 10` exponent                     | `NUMBER → NUMBER`                        | 10^n for n in 0-3 (defaults to 100 otherwise)                                                                                                    |

The helpers that pad and format the amount are local to `Money` and cannot be called on their own.

## Amount conversion

| Function                                       | Signature                                | Description                             |
| ---------------------------------------------- | ---------------------------------------- | --------------------------------------- |
| `major to minor units` majorUnits currencyCode | `NUMBER → STRING → EITHER STRING NUMBER` | Dollars to cents (floors to an integer) |
| `minor to major units` minorUnits currencyCode | `NUMBER → STRING → EITHER STRING NUMBER` | Cents to dollars                        |

## Arithmetic operations

All arithmetic works on minor-unit amounts and validates the currency code first (LEFT for unknown currencies).

| Function                                      | Signature                                         | Description                                         |
| --------------------------------------------- | ------------------------------------------------- | --------------------------------------------------- |
| `add money` amount1 amount2 currencyCode      | `NUMBER → NUMBER → STRING → EITHER STRING NUMBER` | Sum of two amounts                                  |
| `subtract money` amount1 amount2 currencyCode | `NUMBER → NUMBER → STRING → EITHER STRING NUMBER` | Difference of two amounts                           |
| `multiply money` amount factor currencyCode   | `NUMBER → NUMBER → STRING → EITHER STRING NUMBER` | Amount × factor, floored to whole minor units       |
| `divide money` amount divisor currencyCode    | `NUMBER → NUMBER → STRING → EITHER STRING NUMBER` | Amount ÷ divisor, floored; LEFT on division by zero |

## Comparison operations

Plain minor-unit comparisons (both amounts assumed to be in the same currency):

| Function                             | Signature                   | Description        |
| ------------------------------------ | --------------------------- | ------------------ |
| `money equal` amount1 amount2        | `NUMBER → NUMBER → BOOLEAN` | amount1 = amount2  |
| `money greater than` amount1 amount2 | `NUMBER → NUMBER → BOOLEAN` | amount1 > amount2  |
| `money less than` amount1 amount2    | `NUMBER → NUMBER → BOOLEAN` | amount1 < amount2  |
| `money at least` amount1 amount2     | `NUMBER → NUMBER → BOOLEAN` | amount1 >= amount2 |
| `money at most` amount1 amount2      | `NUMBER → NUMBER → BOOLEAN` | amount1 <= amount2 |

## Validation functions

| Function                      | Signature          | Description                                       |
| ----------------------------- | ------------------ | ------------------------------------------------- |
| `is valid currency code` code | `STRING → BOOLEAN` | Exactly 3 characters, all uppercase (format only) |
| `is non-negative` amount      | `NUMBER → BOOLEAN` | amount >= 0                                       |
| `is positive` amount          | `NUMBER → BOOLEAN` | amount > 0                                        |
