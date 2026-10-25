# prelude

General-purpose functions for lists, MAYBE and EITHER values, pairs and dictionaries. Import it with `IMPORT prelude`; it is not loaded automatically.

In the tables, `a`, `b`, `c`, `k`, `v` and `r` stand for any type, as in `GIVEN a IS A TYPE`. The inputs are listed in the order the function takes them.

**Example:** 

```l4-file
-- Prelude Example: Demonstrating core list and Maybe patterns
-- Everything below is core syntax and built-ins, so this file needs no IMPORT.
-- The prelude itself is NOT automatic: reach for one of its functions and you
-- must write `IMPORT prelude` first, exactly as for any other library.

-- List construction using FOLLOWED BY (cons) and EMPTY
DECIDE `first few numbers` IS 1 FOLLOWED BY 2 FOLLOWED BY 3 FOLLOWED BY EMPTY
#EVAL `first few numbers`  -- LIST 1, 2, 3

-- Alternative: LIST syntax
DECIDE `more numbers` IS LIST 4, 5, 6
#EVAL `more numbers`  -- LIST 4, 5, 6

-- Pattern matching on lists with CONSIDER
GIVEN list IS A LIST OF NUMBER
GIVETH A NUMBER
`length of list` list MEANS
  CONSIDER list
  WHEN EMPTY THEN 0
  WHEN x FOLLOWED BY xs THEN 1 PLUS `length of list` xs

#EVAL `length of list` `first few numbers`  -- 3

-- Map: Apply function to each element
GIVEN list IS A LIST OF NUMBER
GIVETH A LIST OF NUMBER
`double all values` list MEANS
  CONSIDER list
  WHEN EMPTY THEN EMPTY
  WHEN x FOLLOWED BY xs THEN (x TIMES 2) FOLLOWED BY `double all values` xs

#EVAL `double all values` `first few numbers`  -- LIST 2, 4, 6

-- Filter: Keep elements matching predicate
GIVEN list IS A LIST OF NUMBER
GIVETH A LIST OF NUMBER
`keep only even numbers` list MEANS
  CONSIDER list
  WHEN EMPTY THEN EMPTY
  WHEN x FOLLOWED BY xs THEN
    IF x MODULO 2 EQUALS 0
    THEN x FOLLOWED BY `keep only even numbers` xs
    ELSE `keep only even numbers` xs

#EVAL `keep only even numbers` (LIST 1, 2, 3, 4, 5, 6)  -- LIST 2, 4, 6

-- Fold: Reduce list to single value (sum)
GIVEN list IS A LIST OF NUMBER
GIVETH A NUMBER
`sum of all values` list MEANS
  CONSIDER list
  WHEN EMPTY THEN 0
  WHEN x FOLLOWED BY xs THEN x PLUS `sum of all values` xs

#EVAL `sum of all values` `first few numbers`  -- 6

-- Maybe type: JUST for present values, NOTHING for absent
DECIDE `maybe has value` IS JUST 42
DECIDE `no value present` IS NOTHING

#EVAL `maybe has value`  -- JUST 42
#EVAL `no value present`     -- NOTHING

-- Pattern matching on Maybe
GIVEN maybe IS A MAYBE NUMBER
GIVETH A NUMBER
`get value or default to zero` maybe MEANS
  CONSIDER maybe
  WHEN NOTHING THEN 0
  WHEN JUST x THEN x

#EVAL `get value or default to zero` `maybe has value`  -- 42
#EVAL `get value or default to zero` `no value present`     -- 0

-- Either type: LEFT or RIGHT
DECIDE `successful result` IS RIGHT 100
DECIDE `error result` IS LEFT "error"

#EVAL `successful result`  -- RIGHT 100
#EVAL `error result`  -- LEFT "error"

-- Element membership check
GIVEN x IS A NUMBER
      list IS A LIST OF NUMBER
GIVETH A BOOLEAN
`is element in list` x list MEANS
  CONSIDER list
  WHEN EMPTY THEN FALSE
  WHEN y FOLLOWED BY ys THEN
    IF x EQUALS y THEN TRUE ELSE `is element in list` x ys

#EVAL `is element in list` 2 `first few numbers`  -- TRUE
#EVAL `is element in list` 9 `first few numbers`  -- FALSE
```



## Lists

### Building and reshaping

| Function    | Inputs                                                                   | Gives                        | What it does                                             |
| ----------- | ------------------------------------------------------------------------ | ---------------------------- | -------------------------------------------------------- |
| `append`    | `list1`, `list2`: LIST OF a                                              | LIST OF a                    | `list1` followed by `list2`                              |
| `concat`    | `lists`: LIST OF LIST OF a                                               | LIST OF a                    | All the lists joined into one                            |
| `reverse`   | `list`: LIST OF a                                                        | LIST OF a                    | The list in reverse order                                |
| `replicate` | `n`: NUMBER, `x`: a                                                      | LIST OF a                    | `n` copies of `x`                                        |
| `range`     | `low`, `high`: NUMBER                                                    | LIST OF NUMBER               | The whole numbers from `low` to `high`, both included    |
| `map`       | `f`: FUNCTION FROM a TO b, `list`: LIST OF a                             | LIST OF b                    | `f` applied to each item                                 |
| `filter`    | `f`: FUNCTION FROM a TO BOOLEAN, `list`: LIST OF a                       | LIST OF a                    | The items for which `f` holds                            |
| `take`      | `n`: NUMBER, `list`: LIST OF a                                           | LIST OF a                    | The first `n` items                                      |
| `drop`      | `n`: NUMBER, `list`: LIST OF a                                           | LIST OF a                    | The list without its first `n` items                     |
| `takeWhile` | `f`: FUNCTION FROM a TO BOOLEAN, `list`: LIST OF a                       | LIST OF a                    | The leading items for which `f` holds                    |
| `dropWhile` | `f`: FUNCTION FROM a TO BOOLEAN, `list`: LIST OF a                       | LIST OF a                    | The list from the first item for which `f` does not hold |
| `zip`       | `list1`: LIST OF a, `list2`: LIST OF b                                   | LIST OF PAIR OF a, b         | The two lists paired item by item                        |
| `zipWith`   | `op`: FUNCTION FROM a AND b TO c, `list1`: LIST OF a, `list2`: LIST OF b | LIST OF c                    | The two lists combined item by item with `op`            |
| `partition` | `f`: FUNCTION FROM a TO BOOLEAN, `list`: LIST OF a                       | PAIR OF LIST OF a, LIST OF a | The items for which `f` holds, and the rest              |
| `sort`      | `list`: LIST OF NUMBER                                                   | LIST OF NUMBER               | Sorted, smallest first                                   |
| `sortBy`    | `cmp`: FUNCTION FROM a AND a TO BOOLEAN, `list`: LIST OF a               | LIST OF a                    | Sorted so that `cmp x y` holds when `x` comes before `y` |
| `insertBy`  | `cmp`: FUNCTION FROM a AND a TO BOOLEAN, `x`: a, `list`: LIST OF a       | LIST OF a                    | `x` inserted into a list already sorted by `cmp`         |
| `nub`       | `list`: LIST OF a                                                        | LIST OF a                    | The list with duplicates removed                         |
| `nubBy`     | `op`: FUNCTION FROM a AND a TO BOOLEAN, `list`: LIST OF a                | LIST OF a                    | Duplicates, as judged by `op`, removed                   |
| `delete`    | `x`: a, `list`: LIST OF a                                                | LIST OF a                    | The list without the first item equal to `x`             |
| `deleteBy`  | `op`: FUNCTION FROM a AND a TO BOOLEAN, `x`: a, `list`: LIST OF a        | LIST OF a                    | The list without the first item that matches `x` by `op` |

### Questions about a list

| Function | Inputs                                             | Gives   | What it does                                               |
| -------- | -------------------------------------------------- | ------- | ---------------------------------------------------------- |
| `null`   | `list`: LIST OF a                                  | BOOLEAN | TRUE if the list is empty                                  |
| `count`  | `list`: LIST OF a                                  | NUMBER  | The number of items                                        |
| `at`     | `list`: LIST OF a, `i`: NUMBER                     | a       | The item at position `i`, counting from 0                  |
| `elem`   | `x`: a, `list`: LIST OF a                          | BOOLEAN | TRUE if `x` is one of the items                            |
| `all`    | `f`: FUNCTION FROM a TO BOOLEAN, `list`: LIST OF a | BOOLEAN | TRUE if `f` holds for every item (TRUE for an empty list)  |
| `any`    | `f`: FUNCTION FROM a TO BOOLEAN, `list`: LIST OF a | BOOLEAN | TRUE if `f` holds for at least one item                    |
| `and`    | `list`: LIST OF BOOLEAN                            | BOOLEAN | TRUE if every item is TRUE                                 |
| `or`     | `list`: LIST OF BOOLEAN                            | BOOLEAN | TRUE if at least one item is TRUE                          |
| `lookup` | `key`: a, `list`: LIST OF PAIR OF a, b             | MAYBE b | The value paired with the first matching key, or `NOTHING` |

`at` stops with an error past the end of the list.

### Folds

| Function | Inputs                                                          | Gives | What it does                                           |
| -------- | --------------------------------------------------------------- | ----- | ------------------------------------------------------ |
| `foldr`  | `cons`: FUNCTION FROM a AND r TO r, `nil`: r, `list`: LIST OF a | r     | Combines the items from the right, starting from `nil` |
| `foldl`  | `op`: FUNCTION FROM r AND a TO r, `ini`: r, `list`: LIST OF a   | r     | Combines the items from the left, starting from `ini`  |

## Numbers

| Function   | Inputs                              | Gives   | What it does                                  |
| ---------- | ----------------------------------- | ------- | --------------------------------------------- |
| `sum`      | `list`: LIST OF NUMBER              | NUMBER  | The total (0 for an empty list)               |
| `product`  | `list`: LIST OF NUMBER              | NUMBER  | The product (1 for an empty list)             |
| `max`      | `x`, `y`: NUMBER                    | NUMBER  | The greater of the two                        |
| `min`      | `x`, `y`: NUMBER                    | NUMBER  | The lesser of the two                         |
| `maximum`  | `list`: LIST OF NUMBER              | NUMBER  | The largest item; an error for an empty list  |
| `minimum`  | `list`: LIST OF NUMBER              | NUMBER  | The smallest item; an error for an empty list |
| `maximum1` | `x`: NUMBER, `list`: LIST OF NUMBER | NUMBER  | The largest of `x` and the items              |
| `minimum1` | `x`: NUMBER, `list`: LIST OF NUMBER | NUMBER  | The smallest of `x` and the items             |
| `even`     | `x`: NUMBER                         | BOOLEAN | TRUE if `x` is even                           |
| `odd`      | `x`: NUMBER                         | BOOLEAN | TRUE if `x` is odd                            |

`max`, `min`, `maximum`, `minimum`, `maximum1` and `minimum1` also accept MAYBE NUMBER values. `NOTHING` counts as absent: the other value wins, and only when every value is `NOTHING`, or the list is empty, is the result `NOTHING`.

With the prelude imported, `<`, `<=`, `>` and `>=` (and `LESS THAN`, `AT MOST`, `GREATER THAN`, `AT LEAST`) also compare two MAYBE NUMBER values. The comparison is FALSE when either side is `NOTHING`.

## MAYBE values

| Function      | Inputs                                                   | Gives     | What it does                                                    |
| ------------- | -------------------------------------------------------- | --------- | --------------------------------------------------------------- |
| `fromMaybe`   | `default`: a, `x`: MAYBE a                               | a         | The value inside `x`, or `default` if `x` is `NOTHING`          |
| `maybe`       | `just`: FUNCTION FROM a TO b, `nothing`: b, `x`: MAYBE a | b         | `just` applied to the value inside `x`, or `nothing`            |
| `orElse`      | `x`, `y`: MAYBE a                                        | MAYBE a   | `x` if it holds a value, otherwise `y`                          |
| `isJust`      | `x`: MAYBE a                                             | BOOLEAN   | TRUE if `x` holds a value                                       |
| `isNothing`   | `x`: MAYBE a                                             | BOOLEAN   | TRUE if `x` is `NOTHING`                                        |
| `asum`        | `list`: LIST OF MAYBE a                                  | MAYBE a   | The first item that holds a value; `firstJust` is the same      |
| `catMaybes`   | `list`: LIST OF MAYBE a                                  | LIST OF a | The values of the items that hold one                           |
| `mapMaybe`    | `f`: FUNCTION FROM a TO MAYBE b, `list`: LIST OF a       | LIST OF b | `f` applied to each item, keeping the results that hold a value |
| `maybeToList` | `x`: MAYBE a                                             | LIST OF a | An empty list or a list of one                                  |
| `listToMaybe` | `list`: LIST OF a                                        | MAYBE a   | The first item, or `NOTHING` for an empty list                  |

## EITHER values

| Function | Inputs                                                                       | Gives | What it does                                                 |
| -------- | ---------------------------------------------------------------------------- | ----- | ------------------------------------------------------------ |
| `either` | `left`: FUNCTION FROM a TO c, `right`: FUNCTION FROM b TO c, `x`: EITHER a b | c     | `left` applied to a `LEFT` value, `right` to a `RIGHT` value |

## Pairs

The prelude declares `PAIR OF a, b` with the fields `fst` and `snd`. Build one with `PAIR OF x, y` and read it with `p's fst` and `p's snd`.

| Function | Inputs                                                  | Gives                | What it does                                                         |
| -------- | ------------------------------------------------------- | -------------------- | -------------------------------------------------------------------- |
| `pmap`   | `f`: FUNCTION FROM b TO c, `p`: PAIR OF a, b            | PAIR OF a, c         | `f` applied to the second value; `mapSnd` is the same                |
| `fmap`   | `f`: FUNCTION FROM b TO c, `list`: LIST OF PAIR OF a, b | LIST OF PAIR OF a, c | `f` applied to the second value of each pair; `mapPairs` is the same |

## Dictionaries

`Dictionary k v` maps keys of type `k` to values of type `v`. It is a record whose `contents` field is a list of `PAIR OF k, v`, with one pair for each key.

### Building a dictionary

| Function          | Inputs                        | Gives                    | What it does                                         |
| ----------------- | ----------------------------- | ------------------------ | ---------------------------------------------------- |
| `emptyDict`       | none                          | Dictionary k v           | A dictionary with no entries                         |
| `singleToDict`    | `k`: a, `v`: b                | Dictionary a b           | One entry; `singleton` is the same                   |
| `pairToDict`      | `pair`: PAIR OF k, v          | Dictionary k v           | One entry from a pair                                |
| `listToDict`      | `pairs`: LIST OF PAIR OF k, v | Dictionary k v           | One entry per pair, as given; `fromList` is the same |
| `fromListGrouped` | `pairs`: LIST OF PAIR OF k, v | Dictionary k (LIST OF v) | The values of each key collected into a list         |

### Reading a dictionary

| Function              | Inputs                                         | Gives                | What it does                      |
| --------------------- | ---------------------------------------------- | -------------------- | --------------------------------- |
| `dictLookup`          | `key`: k, `dict`: Dictionary k v               | MAYBE v              | The value for `key`, or `NOTHING` |
| `dictFindWithDefault` | `default`: v, `key`: k, `dict`: Dictionary k v | v                    | The value for `key`, or `default` |
| `dictMember`          | `key`: k, `dict`: Dictionary k v               | BOOLEAN              | TRUE if `key` has an entry        |
| `dictNotMember`       | `key`: k, `dict`: Dictionary k v               | BOOLEAN              | TRUE if `key` has no entry        |
| `dictKeys`            | `dict`: Dictionary k v                         | LIST OF k            | All keys                          |
| `dictElems`           | `dict`: Dictionary k v                         | LIST OF v            | All values                        |
| `dictToList`          | `dict`: Dictionary k v                         | LIST OF PAIR OF k, v | All entries as pairs              |
| `dictSize`            | `dict`: Dictionary k v                         | NUMBER               | The number of entries             |
| `dictIsEmpty`         | `dict`: Dictionary k v                         | BOOLEAN              | TRUE if there are no entries      |

### Changing a dictionary

Each of these gives a new dictionary; the original is unchanged.

| Function         | Inputs                                                                               | What it does                                                                             |
| ---------------- | ------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------- |
| `dictInsert`     | `key`: k, `value`: v, `dict`: Dictionary k v                                         | Adds the entry, replacing any value for `key`                                            |
| `dictInsertWith` | `combiner`: FUNCTION FROM v AND v TO v, `key`: k, `value`: v, `dict`: Dictionary k v | Adds the entry; if `key` has a value, stores `combiner value` (new, then old)            |
| `dictDelete`     | `key`: k, `dict`: Dictionary k v                                                     | Removes the entry for `key`                                                              |
| `dictAdjust`     | `f`: FUNCTION FROM v TO v, `key`: k, `dict`: Dictionary k v                          | Applies `f` to the value for `key`, if there is one                                      |
| `dictUpdate`     | `f`: FUNCTION FROM v TO MAYBE v, `key`: k, `dict`: Dictionary k v                    | Replaces the value for `key` with what `f` gives, or removes it when `f` gives `NOTHING` |
| `dictUnion`      | `dict1`, `dict2`: Dictionary k v                                                     | Both dictionaries' entries; for a key in both, `dict1`'s value wins                      |
| `dictUnionWith`  | `combiner`: FUNCTION FROM v AND v TO v, `dict1`, `dict2`: Dictionary k v             | Both dictionaries' entries; for a key in both, `combiner` joins the two values           |

All of these give a `Dictionary k v`.

### Working over all entries

| Function            | Inputs                                                                | Gives          | What it does                                   |
| ------------------- | --------------------------------------------------------------------- | -------------- | ---------------------------------------------- |
| `mapDict`           | `f`: FUNCTION FROM v TO w, `dict`: Dictionary k v                     | Dictionary k w | `f` applied to every value                     |
| `dictMapWithKey`    | `f`: FUNCTION FROM k AND v TO w, `dict`: Dictionary k v               | Dictionary k w | `f` applied to every key and value             |
| `filterDict`        | `pred`: FUNCTION FROM v TO BOOLEAN, `dict`: Dictionary k v            | Dictionary k v | The entries whose value satisfies `pred`       |
| `dictFilterWithKey` | `pred`: FUNCTION FROM k AND v TO BOOLEAN, `dict`: Dictionary k v      | Dictionary k v | The entries whose key and value satisfy `pred` |
| `foldrDict`         | `f`: FUNCTION FROM v AND r TO r, `z`: r, `dict`: Dictionary k v       | r              | Combines the values from the right             |
| `foldlDict`         | `f`: FUNCTION FROM r AND v TO r, `z`: r, `dict`: Dictionary k v       | r              | Combines the values from the left              |
| `dictFoldrWithKey`  | `f`: FUNCTION FROM k AND v AND r TO r, `z`: r, `dict`: Dictionary k v | r              | Combines keys and values from the right        |
| `dictFoldlWithKey`  | `f`: FUNCTION FROM r AND k AND v TO r, `z`: r, `dict`: Dictionary k v | r              | Combines keys and values from the left         |

### Grouped pairs

| Function      | Inputs                                                          | Gives                        | What it does                                              |
| ------------- | --------------------------------------------------------------- | ---------------------------- | --------------------------------------------------------- |
| `groupPairs`  | `pairs`: LIST OF PAIR OF k, v                                   | LIST OF PAIR OF k, LIST OF v | Collects the values of each key into a list               |
| `insertValue` | `key`: k, `value`: v, `dictPairs`: LIST OF PAIR OF k, LIST OF v | LIST OF PAIR OF k, LIST OF v | Adds `value` to the list for `key`, creating it if needed |

## Other functions

| Function | Inputs           | Gives  | What it does                                                                         |
| -------- | ---------------- | ------ | ------------------------------------------------------------------------------------ |
| `id`     | `x`: a           | a      | `x` itself                                                                           |
| `const`  | `x`: a, `y`: b   | a      | `x`, ignoring `y`                                                                    |
| `APPEND` | `x`, `y`: STRING | STRING | Written between its inputs, `x APPEND y`: the two strings joined, like `CONCAT x, y` |
| `TBD`    | none             | a      | A placeholder of any type for a value not yet decided; evaluating it is an error     |
