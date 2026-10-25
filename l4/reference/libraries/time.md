# time

Time of day, with no date and no time zone, for rules about opening hours, cut-off times and the like. Import it with `IMPORT time`. Times are kept in 24-hour form; arithmetic wraps around midnight.

**Example:** 

```l4-file
-- Time Example: Wall-clock time operations
IMPORT time

-- Time construction (24-hour format)
DECIDE `office start` IS Time 9 0 0
DECIDE `lunch break` IS Time 12 30
DECIDE `end of day` IS Time 17

#EVAL `office start`   -- 09:00:00
#EVAL `lunch break`    -- 12:30:00
#EVAL `end of day`     -- 17:00:00

-- Time from string (JSON interop) - returns MAYBE TIME
DECIDE `from string` IS Time "14:30:00"
#EVAL `from string`  -- JUST OF (TIME OF 14, 30, 0.0)

-- AM/PM construction
DECIDE `early meeting` IS Time 7 30 am
DECIDE `dinner time` IS Time 7 pm

#EVAL `early meeting`  -- 07:30:00
#EVAL `dinner time`    -- 19:00:00

-- Extracting components
#EVAL `the hour of` `office start`    -- 9
#EVAL `the minute of` `lunch break`   -- 30

-- Time predicates
DECIDE `morning check` IS `office start` `is morning`
DECIDE `afternoon check` IS `lunch break` `is afternoon`
#EVAL `morning check`     -- TRUE
#EVAL `afternoon check`   -- TRUE

-- Time arithmetic (wraps at midnight)
DECIDE `plus two hours` IS `office start` `plus hours` 2
#EVAL `plus two hours`  -- 11:00:00

DECIDE `minus one hour` IS `office start` `minus hours` 1
#EVAL `minus one hour`  -- 08:00:00

-- Time comparisons
DECIDE `start before lunch` IS `office start` LESS THAN `lunch break`
#EVAL `start before lunch`  -- TRUE

DECIDE `start is before lunch` IS `office start` `is before` `lunch break`
DECIDE `lunch is after start` IS `lunch break` `is after` `office start`
#EVAL `start is before lunch`  -- TRUE
#EVAL `lunch is after start`   -- TRUE

#EVAL `the earlier of` `office start` `lunch break`  -- 09:00:00
#EVAL `the later of` `office start` `lunch break`    -- 12:30:00

-- Serial conversion (fraction of day)
#EVAL (Time 12 0 0) `Time to serial`  -- 0.5 (noon = half day)
#EVAL 0.5 `Serial to time`            -- 12:00:00
```



## Constants

| Constant                                    | Value    |
| ------------------------------------------- | -------- |
| `Midnight`                                  | 00:00:00 |
| `Noon`                                      | 12:00:00 |
| `Seconds in a minute`, `Minutes in an hour` | 60       |
| `Hours in a day`                            | 24       |
| `Seconds in an hour`                        | 3600     |
| `Seconds in a day`                          | 86400    |

`Meridiem Indicator` is an enumeration with the values `am` and `pm`.

## Building a time

| Function                      | Gives      | What it does                                             |
| ----------------------------- | ---------- | -------------------------------------------------------- |
| `Time` h m s                  | TIME       | From hours (0 to 23), minutes and seconds                |
| `Time` h m                    | TIME       | Seconds are 0                                            |
| `Time` h                      | TIME       | Minutes and seconds are 0                                |
| `Time` h m s `am` (or `pm`)   | TIME       | 12-hour form; also `Time h m am` and `Time h pm`         |
| `Time` text                   | MAYBE TIME | Reads `"HH:MM:SS"` or `"HH:MM"`                          |
| `Time` datetime               | TIME       | The time part of a DATETIME, ignoring date and time zone |
| serial `` `Serial to time` `` | TIME       | From a fraction of a day: 0 is midnight, 0.5 is noon     |

## Reading a time

| Function                 | Gives   | What it does                    |
| ------------------------ | ------- | ------------------------------- |
| `the hour of` t          | NUMBER  | 0 to 23                         |
| `the minute of` t        | NUMBER  | 0 to 59                         |
| `the second of` t        | NUMBER  | 0 to 59                         |
| t `` `Time to serial` `` | NUMBER  | The time as a fraction of a day |
| t `` `is morning` ``     | BOOLEAN | Before 12:00                    |
| t `` `is afternoon` ``   | BOOLEAN | 12:00 to 17:59                  |
| t `` `is evening` ``     | BOOLEAN | 18:00 or later                  |
| t `` `is before noon` `` | BOOLEAN | Before 12:00                    |
| t `` `is after noon` ``  | BOOLEAN | 12:00 or later                  |

## Moving a time

Each of these wraps around midnight: ``(Time 23 0 0) `plus hours` 2`` is 01:00. Put a time built with `Time` in parentheses, as here, before a word written after it.

| Function                                            | Gives |
| --------------------------------------------------- | ----- |
| t `` `plus hours` `` n, t `` `minus hours` `` n     | TIME  |
| t `` `plus minutes` `` n, t `` `minus minutes` `` n | TIME  |
| t `` `plus seconds` `` n, t `` `minus seconds` `` n | TIME  |

## Comparing times

- `=`, `<`, `<=`, `>`, `>=` and their word forms compare two times.
- t1 `MINUS` t2 is the difference in seconds.
- t1 `` `is before` `` t2 and t1 `` `is after` `` t2 read as sentences.
- `` `the earlier of` `` t1 t2 and `` `the later of` `` t1 t2 pick one of two times.
