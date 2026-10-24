# Class 4: Control Flow, Loops, Functions, Scope

## Objective

Cover `if`/`elif`/`else`, truthiness, `for`/`while` loops, `break`/
`continue`, function arguments (positional, keyword, default, `*args`,
`**kwargs`), and variable scope.

## Files

- `control_flow_loops_functions.ipynb` — if/elif/else, truthiness, loops,
  unpacking, enumerate, functions, argument types, scope.
- `functions.py` — same calculation without a function (for comparison).
- `functions_2.py` — the same calculation refactored into a function.
- `Assignement.txt` — this class's assignment.

## Class notes

### Control flow — if / elif / else

```python
marks = 75

if marks >= 90:
    print("Grade A")
elif marks >= 70:
    print("Grade B")
elif marks >= 50:
    print("Grade C")
else:
    print("Not good")
```

- `elif` lets you check several conditions in sequence; only the first
  matching branch runs.
- **Nested if**: an `if`/`else` can sit inside another `if` block to check
  a second condition only when the first is true.

```python
age = 35
has_id = True

if age >= 18:
    if has_id:
        print("Entry Allowed")
    else:
        print("ID required")
else:
    print("Underage, not allowed")
```

### Truthiness

Python accepts values other than `True`/`False` in a condition — they're
evaluated as **truthy** or **falsy**.

- **Falsy**: `False`, `None`, `0`, `0.0`, `""` (empty string), `[]`, `{}`,
  `set()`, `()` — anything "empty" or zero.
- **Truthy**: `True`, `1`, `1.0`, and generally anything non-empty/non-zero.

### Conditional expression (ternary)

A compact one-line form of if/else that assigns a value directly:

```python
status = "Adult" if age >= 18 else "Minor"
```

Equivalent to the longer:

```python
if age >= 18:
    status = "Adult"
else:
    status = "Minor"
```

### Loops

**`for` with `range()`**: `range(start, stop, step)` — `stop` is
exclusive, `step` defaults to `1`.

```python
for i in range(1, 11, 2):   # 1, 3, 5, 7, 9
    print(i)
```

**`pass` / `continue` / `break`**:

```python
for i in range(0, 11):
    if i in [3, 4, 5]:
        pass          # does nothing, loop continues normally
    else:
        print(i)

for i in range(0, 11):
    if i in [3, 4, 5]:
        continue       # skip the rest of this iteration, go to next i
    print(i)

for i in range(0, 11):
    if i == 3:
        break           # exit the loop entirely
    print(i)
```

**Nested loops** — a loop inside another loop's body, useful when an
outer condition should trigger a separate repeated action:

```python
for i in range(0, 11):
    print(i)
    if i == 7:
        for j in range(0, 300):
            if j == 3:
                break
            print("Welcome to FDE bootcamp")
```

**`while`** — repeats as long as a condition is true; needs an explicit
exit condition to avoid an infinite loop:

```python
i = 0
while True:
    print("hello")
    i = i + 1
    if i > 10:
        break
```

**Looping over a dictionary** — looping directly over a dict gives you
its keys; use `.items()` to get key-value pairs together:

```python
values = {"101": "Tony Stark", "102": "Steve Rogers", "103": "Natasha Romanoff"}

for key in values:                 # keys only
    print(key)

for key, value in values.items():  # key + value together
    print(key, value)
```

**`enumerate()`** — wraps an iterable and also gives you a running count,
starting at `0`, without maintaining a separate counter variable:

```python
for i, pair in enumerate(values.items()):
    key, value = pair     # unpacking the tuple
    print(key, value)
print(f"Loop executed {i + 1} times")
```

**Unpacking** — assigning the elements of a tuple (or any sequence) to
multiple variables in one line:

```python
a1, b1, c1 = (10, 2, 4)
```

### Functions

Without a function, repeated logic means repeated code:

```python
price_1, quantity_1 = 100, 5
total_price_1 = price_1 * quantity_1
print(total_price_1)
```

Wrapping it in a function makes it reusable:

```python
def calculate_total(price, quantity):
    total_price = price * quantity
    return total_price

value_1 = calculate_total(60, 70)
value_2 = calculate_total(1000, 2)
print(value_1 + value_2)
```

### Types of arguments

**Positional** — order determines which parameter gets which value:

```python
def add(a, b):
    return a + b

add(10, 2)      # a=10, b=2
```

**Keyword** — name the parameter explicitly; order no longer matters:

```python
add(b=3, a=2)
```

**Default** — a parameter gets a fallback value if the caller doesn't
supply one. A default parameter must come **after** any positional
parameters in the function definition:

```python
def greet(name, message="great day!"):
    print(message, name)

greet("Tony Stark")          # uses the default message
greet("Steve Rogers", "Hi")  # overrides it
```

**`*args`** — collects any number of extra positional arguments into a
tuple:

```python
def total(*args):
    print(args)        # a tuple of everything passed in
    return sum(args)

total(1, 2, 3, 4, 5)
```

**`**kwargs`** — collects any number of extra keyword arguments into a
dictionary:

```python
def show_user_info(name, phone_no, **kwargs):
    print(kwargs)       # a dict of any extra named arguments
    print(name, phone_no)

show_user_info(phone_no="+983232323", name="Vikesh Singh", email="vikeshsingh@gmail.com")
```

`*args` and `**kwargs` can be combined in one function to accept an
arbitrary mix of positional and keyword arguments.

### Scope

A variable assigned inside a function is **local** to that function by
default — it does not affect a same-named variable outside it:

```python
a = 10

def do_something():
    a = 0        # this is a new local variable, separate from the global a
    a = a + 1
    print(a)     # 1

do_something()
print(a)         # 10 — unchanged
```

**Reading** a global variable inside a function works without any special
keyword. **Reassigning** it requires the `global` keyword:

```python
a = 10

def do_something():
    global a
    a = a + 1
    print(a)

do_something()
print(a)   # 11 — the global was actually changed
```

Without `global`, reassigning a global variable inside a function is not
allowed — but **mutating** a global list/dict/set in place (not
reassigning it) is allowed, since that changes the object itself, not
which object the name points to:

```python
a = [1, 2, 3]

def do_something():
    a.append(123)   # in-place mutation — allowed without `global`
    print(a)

do_something()
print(a)   # [1, 2, 3, 123]
```

## Pending for next class

- Exception handling
- `os` library (`os.path.join`, `os.getcwd`, `os.listdir`, relative vs.
  full paths)
- File handling (with exception handling)
- OOP: class, object, constructor, methods, attributes, `self`
- List/dict comprehensions
- Useful built-ins: `any()`, `all()`, `map()`, `filter()`, `enumerate()`,
  `zip()`, `sorted()`
- `lambda`

## Assignment

Priority 1: exception handling, `os` library basics, file handling (with
exception handling), OOP basics (class, object, constructor, methods,
attributes, `self`).

Priority 2: list/dict comprehensions, `any()`/`all()`/`map()`/`filter()`/
`enumerate()`/`zip()`/`sorted()`, `lambda`.
