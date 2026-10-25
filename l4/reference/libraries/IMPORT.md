> This page has moved to [/l4/reference/libraries](/l4/reference/libraries.md#import).

# Standard libraries

The standard libraries are L4 files that ship with every `l4` binary, the editor extension and Legalese Cloud. They are written in L4 and add functions for lists, dates, money, legal entities and more. Nothing from them is available until you `IMPORT` it, not even the prelude.

Functions built into the language, such as `TOSTRING`, `CONCAT` or `FETCH`, need no import: see [Built-in functions](/l4/reference/builtins.md).

## Libraries

### Core

- [prelude](/l4/reference/libraries/prelude.md): lists, MAYBE and EITHER values, pairs and dictionaries.
- [math](/l4/reference/libraries/math.md): `exp`, logarithms, square roots and trigonometry that give `NOTHING` instead of failing on bad input.

### Dates and time

- [daydate](/l4/reference/libraries/daydate.md): build, compare and shift dates; weekdays, ISO weeks, months and leap years. Its page also covers `date-compat`, the older `DATE d m y` spelling.
- [time](/l4/reference/libraries/time.md): time of day without a date or time zone, in 24-hour or AM/PM form.
- [datetime](/l4/reference/libraries/datetime.md): a date, a time and a time zone together, compared across time zones.
- [timezone](/l4/reference/libraries/timezone.md): constants such as `SGT` and `EST` for IANA time zone names.
- [excel-date](/l4/reference/libraries/excel-date.md): Excel date serials and `DATEDIF`, `DAYS360`, `YEARFRAC`, `EDATE`, `EOMONTH`, `WORKDAY` and `NETWORKDAYS`.

### Money and finance

- [currency](/l4/reference/libraries/currency.md): amounts in whole minor units (cents) with ISO 4217 currency codes.
- [holdings](/l4/reference/libraries/holdings.md): cap tables, vesting, convertible notes and SAFEs, liquidation preferences and valuations.
- [actus](/l4/reference/libraries/actus.md): ACTUS financial contract types, events, day count conventions and schedules.

### Legal entities and places

- [legal-persons](/l4/reference/libraries/legal-persons.md): names, addresses, identity documents, age and legal capacity, corporate entities and beneficial ownership.
- [jurisdiction](/l4/reference/libraries/jurisdiction.md): ISO 3166 country and subdivision codes, with checks and conversions.

### AI

- [llm](/l4/reference/libraries/llm.md): call Anthropic, OpenAI or OpenRouter models over HTTP.

## IMPORT

`IMPORT` makes the definitions of another L4 file available in the current file. Put the `IMPORT` lines at the top of the file, before the first `§` section, one module per line. An `IMPORT` below a section heading is ignored without a warning: the names it should bring in are then reported as [not defined](/l4/reference/errors.md#i-could-not-find-a-definition-for-the-identifier).

**Example:** 

```l4-file
-- IMPORT examples

-- One module per line, at the top of the file.
IMPORT prelude
IMPORT math
IMPORT daydate

-- A name with a hyphen goes in backticks.
IMPORT `legal-persons`

-- prelude: list functions
#EVAL sum (LIST 1, 2, 3)

-- math: functions that give NOTHING instead of failing
#EVAL sqrt 16
#EVAL sqrt (0 - 1)

-- daydate: dates
#EVAL `the day after` (Date 31 12 2024)

-- legal-persons: age on a given date
#EVAL `age in years` (Date 15 3 1990) (Date 1 1 2025)
```



```l4
IMPORT prelude
IMPORT daydate
IMPORT `legal-persons`
```

### Module names

- A module is named by its file name without `.l4`: `IMPORT daydate` loads `daydate.l4`.
- A name with a hyphen goes in backticks: ``IMPORT `excel-date` ``, ``IMPORT `legal-persons` ``, ``IMPORT `date-compat` ``. Without them, `IMPORT excel-date` is a parse error ("unexpected -").
- `IMPORT` takes a module name, not a path or a quoted string. `IMPORT "helpers.l4"` is a parse error, and a folder in front of the name is ignored: ``IMPORT `rules/helpers` `` looks for `helpers.l4`.

### Files cannot import each other in a ring

A file can't import itself, and files can't import each other in a ring: if `a.l4` imports `b.l4`, then `b.l4`, and anything it imports, can't import `a.l4`. See [Import cycle](/l4/reference/errors.md#import-cycle) for how to fix it.

### What an import gives you

- Every top-level definition of the imported file: its types, constructors, functions and constants.
- Nothing is imported for you. A file that uses `map`, `sum` or `fromMaybe` needs `IMPORT prelude` like any other library.
- What a library imports also reaches your file: `IMPORT datetime` gives you `daydate` too, and `IMPORT daydate` gives you `date-compat`. Importing each library you use by name still makes a file easier to read.

### Where modules are found

For `IMPORT helpers`, L4 uses the first of these that exists:

1. `helpers.l4` in your project's root folder, or in the folder of the file that writes the `IMPORT`.
2. The standard library built into the `l4` you are running. It needs no files on disk and always matches that `l4`.

So a file of your own with the name of a standard library, such as `prelude.l4` beside your rules, is used instead of the standard one. Operators and library developers can change the search with the `JL4_LIBRARY_PATH` environment variable; [How IMPORT finds libraries](/l4/reference/libraries/resolution.md) gives the full order, what Legalese Cloud does, and the warning shown when two different copies of a module are visible. When nothing is found, the error is [Module not found](/l4/reference/errors.md#module-not-found).
