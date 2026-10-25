# legal-persons

Legal entity definitions for natural persons and corporate entities: name/address formatting, identity document validation, age and legal capacity checks, citizenship helpers, and beneficial ownership. Import with `` IMPORT `legal-persons` `` (backticks are required because of the hyphen in the name). Also imports prelude, daydate, and jurisdiction.

**Example:** 

```l4-file
-- Legal Persons Example: Natural and corporate entity modeling
IMPORT daydate
IMPORT timezone
IMPORT `legal-persons`

TIMEZONE IS "Etc/UTC"

-- Name formatting
DECIDE `john's full name` IS `full name` "John" "Doe"
#EVAL `john's full name`  -- "John Doe"

DECIDE `jane's formal name` IS `formal name` "Dr." "Jane" "Smith"
#EVAL `jane's formal name`  -- "Dr. Jane Smith"

-- Address formatting
DECIDE `formatted address` IS `format address` "123 Main St" "New York" "NY" "10001" "US"
#EVAL `formatted address`

-- Age calculations
DECIDE `birth date` IS Date 15 6 1990
DECIDE `reference date` IS Date 1 1 2024

DECIDE `age at reference` IS `age in years` `birth date` `reference date`
#EVAL `age at reference`  -- 33

-- Legal capacity checks (varies by jurisdiction)
#EVAL `is adult` `birth date` "US"  -- TRUE (18+)
#EVAL `is adult` `birth date` "JP"  -- TRUE (Japan uses 20)

DECIDE `minor birth date` IS Date 1 1 2010
#EVAL `is adult` `minor birth date` "US"  -- FALSE
#EVAL `is minor` `minor birth date` "US"  -- TRUE

-- Contract capacity
#EVAL `can enter contract` `birth date` "US"  -- TRUE

-- Voting age (varies by country)
#EVAL `can vote` `birth date` "US"  -- TRUE
#EVAL `can vote` `birth date` "BR"  -- TRUE (Brazil allows 16+)

-- Citizenship functions
DECIDE `citizenship list` IS LIST "US", "UK"
#EVAL `has citizenship in` `citizenship list` "US"  -- TRUE
#EVAL `has citizenship in` `citizenship list` "JP"  -- FALSE
#EVAL `has multiple citizenships` `citizenship list`  -- TRUE
#EVAL `citizenship count` `citizenship list`  -- 2

-- Corporate entity types
#EVAL `Corporation type`  -- "CORPORATION"
#EVAL `LLC type`          -- "LLC"

-- Beneficial ownership thresholds
#EVAL `is beneficial owner` 25   -- TRUE (25%+ threshold)
#EVAL `is beneficial owner` 24   -- FALSE
#EVAL `is majority owner` 51     -- TRUE
#EVAL `has significant control` 30  -- TRUE

-- Corporate jurisdiction checks
#EVAL `is US corporation` "US-DE"  -- TRUE (Delaware)
#EVAL `is UK corporation` "GB"     -- TRUE
```



## Name formatting

| Function                                              | Signature                           | Description         |
| ----------------------------------------------------- | ----------------------------------- | ------------------- |
| `full name` firstName lastName                        | `STRING → STRING → STRING`          | "First Last"        |
| `full name with middle` firstName middleName lastName | `STRING → STRING → STRING → STRING` | "First Middle Last" |
| `formal name` prefix firstName lastName               | `STRING → STRING → STRING → STRING` | "Prefix First Last" |

## Address components

| Function                                                  | Signature                                             | Description                |
| --------------------------------------------------------- | ----------------------------------------------------- | -------------------------- |
| `format address` street city state postalCode countryCode | `STRING → STRING → STRING → STRING → STRING → STRING` | Single-line address string |
| `is valid US zip code` postalCode                         | `STRING → BOOLEAN`                                    | Format check               |
| `is valid UK postcode` postalCode                         | `STRING → BOOLEAN`                                    | Format check               |
| `is valid Canadian postal code` postalCode                | `STRING → BOOLEAN`                                    | Format check               |

## Identity document validation

Constants for document type codes (`Passport type`, `National ID type`, `Drivers License type`, `Social Security type`, `Tax ID type`, `Residence Permit type`), plus format validators (length/separator checks only):

| Function                           | Signature          | Expected format       |
| ---------------------------------- | ------------------ | --------------------- |
| `is valid US SSN format` ssn       | `STRING → BOOLEAN` | `XXX-XX-XXXX`         |
| `is valid Canadian SIN format` sin | `STRING → BOOLEAN` | `XXX-XXX-XXX`         |
| `is valid UK NINO format` nino     | `STRING → BOOLEAN` | `AB123456C` (9 chars) |

## Age calculations

| Function                                             | Signature                        | Description                              |
| ---------------------------------------------------- | -------------------------------- | ---------------------------------------- |
| `age in years` birthDate referenceDate               | `DATE → DATE → NUMBER`           | Completed years at the reference date    |
| `current age` birthDate                              | `DATE → NUMBER`                  | Age as of TODAY (requires `TIMEZONE IS`) |
| `is at least age` birthDate minimumAge referenceDate | `DATE → NUMBER → DATE → BOOLEAN` | Age check at a reference date            |
| `is currently at least age` birthDate minimumAge     | `DATE → NUMBER → BOOLEAN`        | Age check as of TODAY                    |

## Legal capacity checks

Jurisdiction-aware (ISO alpha-2 codes; majority age defaults to 18, with e.g. Japan 20 and South Korea 19):

| Function                                        | Signature                 | Description                       |
| ----------------------------------------------- | ------------------------- | --------------------------------- |
| `is adult` birthDate jurisdictionCode           | `DATE → STRING → BOOLEAN` | Has reached the age of majority   |
| `is minor` birthDate jurisdictionCode           | `DATE → STRING → BOOLEAN` | Negation of `is adult`            |
| `can enter contract` birthDate jurisdictionCode | `DATE → STRING → BOOLEAN` | Currently same as `is adult`      |
| `can vote` birthDate jurisdictionCode           | `DATE → STRING → BOOLEAN` | Voting age check (e.g. Brazil 16) |

## Citizenship status

| Function                                      | Signature                           | Description                      |
| --------------------------------------------- | ----------------------------------- | -------------------------------- |
| `is citizen of` citizenshipCode countryCode   | `STRING → STRING → BOOLEAN`         | Code equality                    |
| `has citizenship in` citizenships countryCode | `LIST OF STRING → STRING → BOOLEAN` | Membership in a citizenship list |
| `has multiple citizenships` citizenships      | `LIST OF STRING → BOOLEAN`          | At least two citizenships        |
| `citizenship count` citizenships              | `LIST OF STRING → NUMBER`           | Number of citizenships           |

## Corporate entities

Entity type constants: `Corporation type`, `LLC type`, `LLP type`, `Partnership type`, `Sole Proprietorship type`, `Non-Profit type`, `Cooperative type`, `Trust type`, `Foundation type`. Relationship constants: `Wholly-owned subsidiary relationship`, `Majority-owned subsidiary relationship`, `Minority stake relationship`, `Joint venture relationship`, `Parent company relationship`, `Sister company relationship`, `Affiliated entity relationship`.

**Identifier validation (format checks):**

| Function                         | Signature          | Expected format |
| -------------------------------- | ------------------ | --------------- |
| `is valid US EIN format` ein     | `STRING → BOOLEAN` | `XX-XXXXXXX`    |
| `is valid UK CRN format` crn     | `STRING → BOOLEAN` | 8 characters    |
| `is valid Canadian BN format` bn | `STRING → BOOLEAN` | 9 characters    |

**Status checks (relative to TODAY):**

| Function                                                  | Signature                 | Description                       |
| --------------------------------------------------------- | ------------------------- | --------------------------------- |
| `years since incorporation` incorporationDate             | `DATE → NUMBER`           | Calendar-year difference          |
| `established for at least` incorporationDate minimumYears | `DATE → NUMBER → BOOLEAN` | Age-of-entity check               |
| `is dissolved` incorporationDate dissolutionDate          | `DATE → DATE → BOOLEAN`   | Dissolution date is in the past   |
| `is active corporation` incorporationDate                 | `DATE → BOOLEAN`          | Incorporation date is in the past |

## Beneficial ownership

| Function                                      | Signature          | Threshold |
| --------------------------------------------- | ------------------ | --------- |
| `is beneficial owner` ownershipPercentage     | `NUMBER → BOOLEAN` | >= 25%    |
| `is majority owner` ownershipPercentage       | `NUMBER → BOOLEAN` | > 50%     |
| `is controlling owner` ownershipPercentage    | `NUMBER → BOOLEAN` | > 50%     |
| `has significant control` ownershipPercentage | `NUMBER → BOOLEAN` | >= 25%    |

## Corporate jurisdiction

| Function                                                                       | Signature                   | Description                                           |
| ------------------------------------------------------------------------------ | --------------------------- | ----------------------------------------------------- |
| `is Delaware corporation` jurisdictionCode                                     | `STRING → BOOLEAN`          | Code equals `"US-DE"`                                 |
| `is US corporation` / `is UK corporation` / `is Canadian corporation`          | `STRING → BOOLEAN`          | Country prefix / constituent-country checks           |
| `requires registered agent` jurisdictionCode                                   | `STRING → BOOLEAN`          | US, UK, or Canadian corporation                       |
| `is foreign qualification required` businessJurisdiction operatingJurisdiction | `STRING → STRING → BOOLEAN` | Different US jurisdictions for business vs operations |
