# Class 3: Sets, Dictionaries, Typecasting, I/O, Copying, Strings

## Objective

Finish collections (set, dictionary), cover typecasting, input/output,
reference vs. copy semantics, and string operations in depth.

## Files

- `collection_and_strings.ipynb` — sets, dictionaries, typecasting,
  reference/shallow/deep copy, string operations.
- `print_demo.py` — nested dictionary example with `.get()`.
- `input_demo.py` — reading and typecasting numeric input.
- `assignment.txt` — this class's assignment.

## Class notes

### Set

Creation:

```python
numbers = {1, 2, 3}
numbers2 = set()          # empty set — {} creates a dict, not a set
```

- `numbers.add(10)` — add a value.
- `numbers.discard(3)` — remove a value, no error if missing (prefer this
  over `remove()`, which raises an error if the value isn't present).
- `remove()` exists too, but needs a try/except to be safe in production.

**Set theory ops** (useful for "what's common/different between two
groups" problems — e.g. comparing names detected across two camera feeds):

```python
a = {1, 2, 3, 4, 5}
b = {3, 4, 5}

a | b     # union — all unique elements from both
a & b     # intersection — only elements common to both
a - b     # difference — elements in a but not in b
```

### Dictionary

Stores data as key-value pairs. Keys must be unique and hashable (can't be
a list). Values can be anything, including another dictionary.

```python
student = {
    "name": "hello",
    "id": 101,
}
```

- Access: `student["name"]` or `student.get("name")`.
  - `[]` raises an error if the key doesn't exist; `.get()` returns `None`
    (or a default you supply) instead — prefer `.get()` in production.
- Re-assigning an existing key overwrites its value (keys stay unique; the
  latest write wins).
- Nesting: a dictionary value can itself be a dictionary — e.g.
  `{101: {"name": "A", "id": 101}, 102: {"name": "B", "id": 102}}` — a far
  better structure than parallel lists (`names`, `ids`, `classes`) kept in
  sync by index.

Common methods:

```python
student.keys()       # all keys
student.values()     # all values
student.items()      # (key, value) pairs
student.pop(101)     # remove by key, returns the removed value
student.update({102: {...}})   # merge another dict in — adds new keys,
                                 # overwrites matching existing keys
student.clear()      # empty the dictionary
```

`update()` doesn't edit a value in place — it merges a new dict in, and
on a key collision the new value replaces the old one entirely. To update
one field of a nested value, target it directly:
`student[102]["name"] = "New Name"`.

### Typecasting

Converting a value from one type to another using the type's constructor
function: `int(...)`, `float(...)`, `str(...)`, `list(...)`, `set(...)`.

```python
float(12)        # 12.0
int(float("12.5"))   # "12.5" -> float -> int; int("12.5") directly errors
list("abc")       # ['a', 'b', 'c'] — string is a sequence, so this splits
                   #  into characters
list({1, 2, 3})   # set -> list
set([1, 1, 2])    # list -> set, drops duplicates
```

A string containing a decimal (`"12.5"`) can't go straight to `int()` —
convert to `float` first, then `int`. Converting to `list`/`set` requires
the source to be sequential/iterable (a plain number isn't).

### Input / output

`print()` writes to the terminal/console. `input()` reads from the user —
and **always returns a string**, regardless of what was typed.

```python
value = input("Enter a number: ")
number = float(input("Enter a number: "))   # typecast immediately if you
                                              # need to do math with it
```

Adding two raw `input()` results with `+` concatenates strings
(`"100" + "200"` → `"100200"`), not adds numbers — typecast first.

### Reference, shallow copy, deep copy

A plain `b = a` makes `b` point to the exact same object as `a` — they
share one address, so a mutation through either name is visible through
both.

```python
a = [1, 2]
b = a
b.append(3)
print(a)     # [1, 2, 3] — a changed too, same object
```

`a.copy()` (**shallow copy**) creates a new outer list, but for a *nested*
list, the inner elements are still shared references:

```python
a = [[1, 2], [4, 5]]
b = a.copy()
a[0].append(456)
print(b)     # the inner list changed in b too — shallow copy only
             # copies the outer container
```

For a fully independent copy of nested structures, use `copy.deepcopy()`:

```python
import copy
b = copy.deepcopy(a)
a[0].append(456)
print(b)     # unaffected — every level was copied
```

### Indexing and slicing (applies to any sequence: string, list, tuple)

Indexes start at `0` from the left; negative indexes count from `-1` at
the right end.

```python
s = "FDE"
s[0]        # "F"
s[-1]       # last character — more robust than len(s) - 1
```

Slicing: `sequence[start:end:step]` — `start` is inclusive, `end` is
**exclusive**, `step` defaults to `1`.

```python
s = "forward"
s[0:3]      # "for" — indexes 0,1,2 (3 excluded)
s[:]        # full copy — missing start defaults to 0, missing end
             # defaults to the sequence's length
s[0:6:2]    # every 2nd character from 0 to 5
s[::-1]     # reversed — step -1 walks right to left
```

### String basics

- Single, double, or triple quotes all create strings; triple quotes also
  allow a string to span multiple lines.
- A triple-quoted block **not** assigned to a variable is treated as a
  comment by the interpreter, even though it looks like a string.
- `#` starts a single-line comment.
- Strings are immutable (same as covered for other immutable types).

### String operators and methods

```python
"hi" * 3                 # "hihihi" — * repeats a string
"a-b-c".split("-")        # ["a", "b", "c"] -> list
"-".join(["a", "b", "c"])  # "a-b-c" -> string, joins list items with a separator
"a,b,c".replace(",", " ")  # "a b c"
"  Name@EXAMPLE.com  ".strip().lower()   # trims whitespace, lowercases
"abc123".isdigit()        # False — not ALL characters are digits
"123".isdigit()           # True
"abc123".isalnum()        # True — letters and/or digits only
"abc".islower()           # True
"ABC".isupper()           # True
```

`split()` without an argument also splits on whitespace — a simple way to
count words: `len(text.split())`.

**f-strings** — the preferred way to build strings from variables:

```python
name = "Alex"
dob = "17101970"
f"{name}'s date of birth is {dob}"

# number formatting: limit to 2 decimal places
value = 3.14159
f"{value:.2f}"     # "3.14"
```

Plain concatenation (`name + " date of birth is " + dob`) and passing
multiple arguments to `print()` both work, but f-strings are clearer and
are what you should reach for in real code.

**Escape characters**: `\n` = new line, `\t` = tab — used inside a string
literal to control formatting when printed.

**Practical pattern** — splitting an email to get the domain:

```python
email = "name@gmail.com"
provider = email.split("@")[-1]    # "gmail.com"
```

Prefer `[-1]` over a fixed positive index like `[1]` when splitting:
`[-1]` always returns the last piece even if the split produced fewer
parts than expected, where a fixed index can raise an error.

## Pending for next class

- Control flow: `if` / `elif` / `else`
- `for` and `while` loops
- Functions (part 2, following on from this class)

## Assignment

- Research what a **hash** is (used internally by dictionaries/sets for
  key lookup).
- Optional: look into `str.format()` as an older alternative to f-strings.
- Complete `if`/`elif`/`else`, `for`/`while` loops, and review functions
  before the next class.
