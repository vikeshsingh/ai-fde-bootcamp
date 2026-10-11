# Class 2: Python Fundamentals

## Objective

Recap core Python: variables, data types, operators, and the first three
collection types (list, tuple, set). Dictionary and string operations carry
over to the next class.

## Files

- `variables_data_types.ipynb` — variables, data types, mutability.
- `python_operators.ipynb` — arithmetic, comparison, assignment, logical,
  identity, and bitwise operators.
- `collection.ipynb` — list, tuple, and set.
- `screenshot/` — reference screenshots (binary/bitwise operator examples).

## Class notes

### Variables

A variable is a name that refers to an object in memory — it does not store
the value itself, it stores the address where the value lives.

```python
a = 3
print(a)        # 3
print(id(a))    # numeric representation of the address a points to
```

**Naming rules:**
- Letters, digits, and underscores only; cannot start with a digit.
- No spaces.
- Use **snake_case** (lowercase words separated by underscores) —
  `first_name`, not `firstName` or `first name`.
- Names should be meaningful (`age`, not `a`).

### Data types

| Category | Types |
|---|---|
| Number | `int`, `float`, `complex` (rarely used) |
| Boolean | `bool` — `True` / `False` only (capitalized) |
| Text | `str` — sequence of characters, single or double quotes |
| Sequence | `list`, `tuple`, `range` |
| Set | `set` |
| Mapping | `dict` |
| Null | `NoneType` — the value `None` |

Python infers the type automatically — no explicit type declaration needed.
Use `type(x)` to check a value's type.

```python
num_integer1 = 12
print(type(num_integer1))   # <class 'int'>

num_float = 12.0            # any decimal point makes it a float
print(type(num_float))      # <class 'float'>
```

### Mutable vs. immutable

- **Immutable** (a new value always gets a new memory address): `int`,
  `float`, `bool`, `str`, `tuple`.
- **Mutable** (same address, contents change in place): `list`, `dict`, `set`.

```python
a = 10
print(id(a))
a = a + 1       # new address — int is immutable
print(id(a))    # different from before

nums = [10]
print(id(nums))
nums.append(20) # same address — list is mutable
print(id(nums))
```

### Primitive vs. non-primitive (borrowed terminology)

Python doesn't formally distinguish these, but by convention:
- **Primitive**: `int`, `float`, `bool`, `str`, `NoneType`.
- **Non-primitive**: `list`, `tuple`, `set`, `dict`.

### Operators

**Arithmetic**

| Operator | Meaning | Example | Result |
|---|---|---|---|
| `+` | Addition | `10 + 3` | `13` |
| `-` | Subtraction | `10 - 3` | `7` |
| `*` | Multiplication | `10 * 3` | `30` |
| `/` | True division (keeps decimal) | `10 / 3` | `3.333...` |
| `//` | Floor division (drops decimal) | `10 // 3` | `3` |
| `%` | Modulus (remainder) | `10 % 3` | `1` |
| `**` | Exponent | `10 ** 3` | `1000` |

**Comparison** — always returns a boolean:

`==` equal to, `!=` not equal to, `>` greater than, `<` less than,
`>=` greater or equal, `<=` less or equal.

**Assignment**

`=`, and compound forms `+=`, `-=`, `*=`, `/=`, `//=`, `%=`, `**=` — e.g.
`x += 5` unwraps to `x = x + 5`.

**Logical** (works on booleans only)

- `and` — `True` only if both operands are `True`.
- `or` — `True` if at least one operand is `True`.
- `not` — flips the boolean.

```python
age = 25
has_id = True
can_enter = age > 18 and has_id   # True
```

**Identity**

- `is` / `is not` — checks whether two names point to the *same* object in
  memory (not whether values are equal).

```python
a = 10
b = 20
c = 10
print(a is b)   # False — different objects
print(a is c)   # True — Python reuses the same small-int object
```

**Bitwise** (rarely needed day-to-day, occasionally used for optimization)

`&` (AND), `|` (OR), `~` (NOT), `<<` (left shift), `>>` (right shift) —
operate on the binary representation of numbers.

**Operator precedence** (high to low): parentheses → exponent → unary
(`not`, bitwise NOT) → multiplication/division/floor-division/modulus →
addition/subtraction → bitwise shifts/bitwise ops → comparisons → `not` →
`and` → `or`. When in doubt, use parentheses to force the order explicitly.

### Collections

| Type | Ordered | Mutable | Duplicates |
|---|---|---|---|
| `list` | Yes | Yes | Allowed |
| `tuple` | Yes | No | Allowed |
| `set` | No | Yes | Not allowed |
| `dict` | Yes | Yes | Keys must be unique |

**List** — `[]`, general-purpose ordered collection:

```python
numbers = [10, 20, 30]
numbers.append(1000)        # add at end
numbers.remove(30)          # remove first match by value
numbers.pop(0)               # remove and return value at index
numbers.insert(1, 5)         # insert value at index
numbers.index(20)            # first index of value
numbers.count(10)            # occurrences of value
numbers.sort()                # sort in place (same-type comparable elements only)
numbers.reverse()             # reverse in place
len(numbers)                  # length
```

Indexing starts at `0` from the left. `numbers[2]` retrieves the value at
index 2.

**Tuple** — `()`, ordered and immutable. Use it for data that should never
change (e.g. days of the week). A single-element tuple needs a trailing
comma: `(1,)` — without it, `(1)` is just the int `1`.

```python
tuple_1 = (1, 2, 3, 4, 5, 6)
tuple_1[2]        # 3 — reading is fine
# tuple_1[2] = 100  # not allowed — immutable
```

**Set** — `{}`, unordered, mutable, no duplicates:

```python
numbers = {1, 1, 2, 3, 4, 5}   # duplicates collapse automatically
numbers.add(4)
numbers.discard(400)            # removes by value if present, no error if not
```

Sets have no positional indexing — you can't do `my_set[0]`.
