# datetime

Absolute points in time: a date, a time of day and a time zone together, for deadlines that fall at a particular moment and for comparisons across time zones. Import it with `IMPORT datetime`; that also gives you [daydate](/l4/reference/libraries/daydate.md) (for `Date`) and `date-compat`. Import `time` as well if you build `TIME` values with `Time`.

A DATETIME is stored as a moment in UTC together with its IANA time zone name. The functions that read it give local values in that time zone.

**Example:** 

```l4-file
-- DateTime Example: Absolute points in time with timezones
IMPORT datetime
IMPORT timezone
IMPORT daydate

TIMEZONE IS SGT


-- DateTime construction from DATE + time components (uses document timezone SGT by default)
DECIDE `the deadline` IS Datetime (Date 31 12 2025) 17 0 0
#EVAL `the deadline`  -- 2025-12-31T17:00:00+08:00

-- With explicit timezone override
DECIDE `london meeting` IS Datetime (DATE_FROM_DMY 1 6 2026) (TIME_FROM_HMS 9 0 0) "Europe/London"
#EVAL `london meeting`  -- 2026-06-01T09:00:00+01:00

-- Date-only (midnight in document timezone)
DECIDE `start of year` IS Datetime (Date 1 1 2026)
#EVAL `start of year`  -- 2026-01-01T00:00:00+08:00


-- DateTime construction from numbers (DMY order, like Date d m y)
DECIDE `full datetime` IS Datetime 31 12 2025 17 30 0
#EVAL `full datetime`  -- 2025-12-31T17:30:00+08:00

DECIDE `date and hour` IS Datetime 15 6 2025 14
#EVAL `date and hour`  -- 2025-06-15T14:00:00+08:00

DECIDE `date only` IS Datetime 25 12 2025
#EVAL `date only`  -- 2025-12-25T00:00:00+08:00

DECIDE `month and year` IS Datetime 6 2025
#EVAL `month and year`  -- 2025-06-01T00:00:00+08:00

DECIDE `year only` IS Datetime 2025
#EVAL `year only`  -- 2025-01-01T00:00:00+08:00

-- Number constructors with explicit timezone
DECIDE `nyc new year` IS Datetime 1 1 2026 0 0 0 EST
#EVAL `nyc new year`  -- 2026-01-01T00:00:00-05:00


-- DateTime from ISO-8601 string (JSON interop) - returns MAYBE DATETIME
DECIDE `from iso` IS Datetime "2025-06-15T10:30:00Z"
#EVAL `from iso`  -- JUST OF (2025-06-15T18:30:00+0800)

-- ISO string with explicit display timezone - returns MAYBE DATETIME
DECIDE `from iso tz` IS Datetime "2025-06-15T10:30:00Z" SGT
#EVAL `from iso tz`  -- JUST OF (2025-06-15T18:30:00+0800)


-- Extracting components (returns local values)
DECIDE `meeting` IS Datetime (Date 15 6 2025) (TIME_FROM_HMS 14 30 0)
#EVAL `Date of` `meeting`      -- the date component
#EVAL `Time of` `meeting`      -- the time component
#EVAL `Timezone of` `meeting`  -- "Asia/Singapore"
#EVAL `the hour of` `meeting`  -- 14
#EVAL `the day of` `meeting`   -- 15

-- Predicates
DECIDE `afternoon check` IS `meeting` `is afternoon`
#EVAL `afternoon check`  -- TRUE

-- Relative operations
#EVAL `at midnight` `meeting`  -- same date at 00:00:00
#EVAL `at noon` `meeting`      -- same date at 12:00:00

-- Cross-timezone comparison (compares UTC instants)
DECIDE `sg morning` IS Datetime (Date 1 6 2026) 8 0 0 SGT
DECIDE `london midnight` IS Datetime (Date 1 6 2026) 0 0 0 "Europe/London"
DECIDE `sg is later` IS `sg morning` GREATER THAN `london midnight`
#EVAL `sg is later`  -- TRUE (SGT 08:00 = UTC 00:00, London 00:00 BST = UTC 23:00 prev day)

-- DateTime comparisons
DECIDE `before deadline` IS `meeting` LESS THAN `the deadline`
#EVAL `before deadline`  -- TRUE

DECIDE `meeting is before deadline` IS `meeting` `is before` `the deadline`
DECIDE `deadline is after meeting` IS `the deadline` `is after` `meeting`
#EVAL `meeting is before deadline`  -- TRUE
#EVAL `deadline is after meeting`   -- TRUE

#EVAL `the earlier of` `meeting` `the deadline`
#EVAL `the later of` `meeting` `the deadline`
```



## The document time zone

The forms of `Datetime` without a time zone use the document's time zone, so the file needs a `TIMEZONE IS` line. The [timezone](/l4/reference/libraries/timezone.md) library has constants for common zones:

```l4
IMPORT datetime
IMPORT timezone

TIMEZONE IS SGT
```

## Building a DATETIME

Every form below also takes a time zone as a last input, an IANA name such as `"Europe/Paris"`, to use instead of the document's.

| Function                | Gives          | What it does                                         |
| ----------------------- | -------------- | ---------------------------------------------------- |
| `Datetime` date time    | DATETIME       | A DATE and a TIME                                    |
| `Datetime` date h m s   | DATETIME       | A DATE and the hour, minute and second               |
| `Datetime` date         | DATETIME       | Midnight at the start of the date                    |
| `Datetime` d m y h mn s | DATETIME       | Day, month, year, hour, minute and second            |
| `Datetime` d m y h mn   | DATETIME       | Seconds are 0                                        |
| `Datetime` d m y h      | DATETIME       | Minutes and seconds are 0                            |
| `Datetime` d m y        | DATETIME       | Midnight on that day                                 |
| `Datetime` m y          | DATETIME       | Midnight on the 1st of the month                     |
| `Datetime` y            | DATETIME       | Midnight on 1 January                                |
| `Datetime` text         | MAYBE DATETIME | Reads ISO 8601 text such as `"2024-06-15T10:30:00Z"` |
| `Datetime` text tz      | MAYBE DATETIME | The same, shown in the time zone `tz`                |

The number forms take the day before the month, as in `Datetime 15 6 2024 10 30 0`.

## Reading a DATETIME

| Function                                                        | Gives   | What it does                                                |
| --------------------------------------------------------------- | ------- | ----------------------------------------------------------- |
| `Date of` dt                                                    | DATE    | The local date                                              |
| `Time of` dt                                                    | TIME    | The local time of day                                       |
| `Timezone of` dt                                                | STRING  | The IANA time zone name                                     |
| `the hour of` dt                                                | NUMBER  | Local hour, 0 to 23                                         |
| `the minute of` dt                                              | NUMBER  | Local minute                                                |
| `the second of` dt                                              | NUMBER  | Local second                                                |
| `the day of` dt                                                 | NUMBER  | Local day of the month                                      |
| `the month of` dt                                               | NUMBER  | Local month                                                 |
| `the year of` dt                                                | NUMBER  | Local year                                                  |
| dt `` `Datetime to serial` ``                                   | NUMBER  | A serial number counted in UTC                              |
| dt `` `is morning` ``, `` `is afternoon` ``, `` `is evening` `` | BOOLEAN | Before 12:00; 12:00 to 17:59; 18:00 or later, in local time |
| dt `` `is before noon` ``, `` `is after noon` ``                | BOOLEAN | Before 12:00; 12:00 or later                                |

## Moving a DATETIME

| Function           | Gives    | What it does                                  |
| ------------------ | -------- | --------------------------------------------- |
| `at midnight` dt   | DATETIME | The same date at 00:00, in the same time zone |
| `at noon` dt       | DATETIME | The same date at 12:00                        |
| dt `` `at` `` time | DATETIME | The same date at another time of day          |

## Comparing DATETIME values

Comparisons use the moment in UTC, so 09:00 in Singapore is before 09:00 in London on the same day.

- `=`, `<`, `<=`, `>`, `>=` and their word forms compare two DATETIME values.
- dt1 `` `is before` `` dt2 and dt1 `` `is after` `` dt2 read as sentences.
- `` `the earlier of` `` dt1 dt2 and `` `the later of` `` dt1 dt2 pick one of two values.
