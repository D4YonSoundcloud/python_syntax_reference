# Python Syntax Reference

A single-page lookup for Python syntax, built-in types, standard library signatures, and general-purpose idioms.

Everything here is language reference material — nothing is specific to any problem, framework, or domain.

**How to use this page.** It is deliberately one long file so browser find (`Ctrl+F` / `Cmd+F`) is the search tool. Search for the method name — `setdefault`, `startswith`, `bisect` — rather than a description.

`# →` marks the value an expression produces. Every one has been machine-checked.

---

## Contents

[Operators](#operators) · [Numbers](#numbers) · [Strings](#strings) · [Formatting](#string-formatting) · [Lists](#lists) · [Slicing](#slicing) · [Dictionaries](#dictionaries) · [Sets](#sets) · [Tuples](#tuples) · [Comprehensions](#comprehensions) · [Control flow](#control-flow) · [Functions](#functions) · [Classes](#classes) · [Exceptions](#exceptions) · [Sorting](#sorting) · [Iteration](#iteration-helpers) · [Built-ins](#built-in-functions) · [Standard library](#standard-library) · [Conversion](#type-conversion) · [Idioms](#general-idioms) · [Gotchas](#gotchas) · [Versions](#version-notes)

---

## Operators

| Operator | Meaning | Note |
|---|---|---|
| `+ - * /` | arithmetic | `/` always produces a float |
| `//` | floor division | rounds toward negative infinity |
| `%` | remainder | sign follows the right operand |
| `**` | power | right-associative |
| `== !=` | value equality | works on lists, tuples, dicts |
| `is`, `is not` | identity | use only for `None`, `True`, `False` |
| `< <= > >=` | ordering | chainable: `0 <= x < 10` |
| `and or not` | boolean | short-circuiting |
| `in`, `not in` | membership | O(1) on dict/set, O(n) on list |
| `& \| ^ ~` | bitwise, and set algebra | |
| `<< >>` | bit shift | |
| `:=` | assignment expression | 3.8+ |

Precedence, highest first: `**` → unary `- + ~` → `* / // %` → `+ -` → `<< >>` → `&` → `^` → `|` → comparisons / `in` / `is` → `not` → `and` → `or`.

```python
2 + 3 * 4                   # → 14
(2 + 3) * 4                 # → 20
-2 ** 2                     # → -4        ** binds tighter than unary minus
(-2) ** 2                   # → 4
2 ** 3 ** 2                 # → 512       right-associative: 2 ** (3 ** 2)
7 // 2                      # → 3
7 % 2                       # → 1
```

---

## Numbers

| Task | Code | Notes |
|---|---|---|
| floor division | `a // b` | rounds toward −∞, not toward zero |
| remainder | `a % b` | |
| both at once | `divmod(a, b)` | returns a tuple |
| power | `a ** b` | |
| absolute value | `abs(x)` | |
| round to n places | `round(x, n)` | ties round to even |
| round up | `-(-a // b)` | negate, floor, negate |
| clamp | `max(lo, min(hi, x))` | |
| mean | `sum(xs) / len(xs)` | |
| sign as −1/0/1 | `(x > 0) - (x < 0)` | |
| infinity | `float("inf")` | |
| parse in another base | `int(s, 16)` | |

```python
a, b = 17, 5

a / b                       # → 3.4       true division, always float
a // b                      # → 3         floor division, stays int
a % b                       # → 2
divmod(a, b)                # → (3, 2)
a ** 2                      # → 289
abs(-9)                     # → 9

-17 // 5                    # → -4        floors toward -infinity
int(-17 / 5)                # → -3        int() truncates toward zero

round(3.14159, 2)           # → 3.14
round(2.5)                  # → 2         ties go to even
round(3.5)                  # → 4

max(3, 9, 2)                # → 9
min([3, 9, 2])              # → 2
sum([1, 2, 3])              # → 6
sum([1, 2, 3], 10)          # → 16        with a start value

float("inf") > 10 ** 100    # → True
int("42")                   # → 42
int("ff", 16)               # → 255
bin(5)                      # → '0b101'
hex(255)                    # → '0xff'

0.1 + 0.2 == 0.3            # → False     binary float representation
abs((0.1 + 0.2) - 0.3) < 1e-9   # → True
```

Integers are arbitrary precision. Floats are IEEE 754 doubles.

---

## Strings

Immutable — every method returns a new string.

| Method | Returns |
|---|---|
| `s.startswith(prefix[, start[, end]])` | bool; `prefix` may be a tuple |
| `s.endswith(suffix[, start[, end]])` | bool |
| `s.find(sub)` / `rfind` | index, or `-1` if absent |
| `s.index(sub)` / `rindex` | index; raises `ValueError` |
| `s.count(sub)` | occurrences |
| `s.replace(old, new[, count])` | new string |
| `s.split([sep[, maxsplit]])` | list; no arg splits on any whitespace run |
| `s.rsplit`, `s.splitlines()` | list |
| `s.join(iterable)` | string |
| `s.strip([chars])`, `lstrip`, `rstrip` | trimmed |
| `s.lower() upper() title() capitalize() swapcase()` | new string |
| `s.zfill(w)`, `ljust(w[, c])`, `rjust(w[, c])`, `center(w)` | padded |
| `s.isdigit() isalpha() isalnum() isspace() islower() isupper()` | bool |
| `s.removeprefix(p)` / `removesuffix(s)` | 3.9+ |

```python
s = "  Hello, World  "

s.strip()                       # → 'Hello, World'
s.strip().lower()               # → 'hello, world'
"a-b-c".split("-")              # → ['a', 'b', 'c']
"a b  c".split()                # → ['a', 'b', 'c']
"-".join(["a", "b"])            # → 'a-b'
"abc".startswith(("x", "a"))    # → True        a tuple matches any
"hello".find("zz")              # → -1          never raises
"hello".index("ll")             # → 2           raises if absent
"hello".replace("l", "L", 1)    # → 'heLlo'
"7".zfill(3)                    # → '007'
"ab".ljust(5, ".")              # → 'ab...'
"123".isdigit()                 # → True
"a,b".count(",")                # → 1

w = "abcdef"
w[0]                            # → 'a'
w[-1]                           # → 'f'
w[:3]                           # → 'abc'
w[::-1]                         # → 'fedcba'    reverse
len(w)                          # → 6
"-" * 3                         # → '---'
list("abc")                     # → ['a', 'b', 'c']
"".join(sorted("cba"))          # → 'abc'
ord("a")                        # → 97
chr(97)                         # → 'a'

len(r"a\nb")                    # → 4           raw string, backslash is literal

multi = """line1
line2"""
multi.splitlines()              # → ['line1', 'line2']
```

---

## String formatting

f-strings (3.6+) are the default. Spec: `{value:[[fill]align][sign][#][0][width][,][.precision][type]}`

| Spec | Meaning | Result |
|---|---|---|
| `:d` | integer | `5` |
| `:03d` | zero-padded, width 3 | `007` |
| `:.2f` | fixed point, 2 places | `3.14` |
| `:,` | thousands separator | `1,234` |
| `:>10` `:<10` `:^10` | right / left / centre align | |
| `:+d` | always show the sign | `+5` |
| `:.3e` | scientific | `1.235e+03` |
| `:b` `:o` `:x` `:X` | binary / octal / hex | |
| `!r` | use `repr()` | |

```python
name, n, x = "ada", 7, 3.14159

f"{name}({n})"              # → 'ada(7)'
f"{n:03d}"                  # → '007'
f"{x:.2f}"                  # → '3.14'
f"{1234567:,}"              # → '1,234,567'
f"{name:>6}"                # → '   ada'
f"{name:<6}|"               # → 'ada   |'
f"{name:^7}|"               # → '  ada  |'
f"{n:+d}"                   # → '+7'
f"{255:x}"                  # → 'ff'
f"{name!r}"                 # → "'ada'"
f"{n * 2}"                  # → '14'        expressions are allowed
f"{'yes' if n else 'no'}"   # → 'yes'

"{}({})".format(name, n)    # → 'ada(7)'
"{0}-{0}".format(name)      # → 'ada-ada'
"%s(%d)" % (name, n)        # → 'ada(7)'
```

---

## Lists

| Method | Effect |
|---|---|
| `xs.append(v)` | add one item at the end |
| `xs.extend(iterable)` | add many |
| `xs.insert(i, v)` | insert before index `i` |
| `xs.pop([i])` | remove and return; default last |
| `xs.remove(v)` | remove the first equal item; raises `ValueError` |
| `xs.index(v[, start[, end]])` | first index; raises `ValueError` |
| `xs.count(v)` | occurrences |
| `xs.sort(*, key=None, reverse=False)` | **in place, returns `None`** |
| `xs.reverse()` | in place, returns `None` |
| `xs.copy()` | shallow copy, same as `list(xs)` |
| `xs.clear()` | empty it |

```python
xs = [3, 1, 4]

xs.append(1)
xs                          # → [3, 1, 4, 1]
xs.extend([5, 9])
xs                          # → [3, 1, 4, 1, 5, 9]
xs.insert(0, 0)
xs                          # → [0, 3, 1, 4, 1, 5, 9]
xs.pop()                    # → 9
xs.pop(0)                   # → 0
xs.remove(1)                              # removes the FIRST match only
xs                          # → [3, 4, 1, 5]
xs.index(1)                 # → 2
xs.count(1)                 # → 1
xs.sort()                   # → None      sorts in place
xs                          # → [1, 3, 4, 5]
xs.reverse()
xs                          # → [5, 4, 3, 1]

[1, 2] + [3]                # → [1, 2, 3]
[0] * 3                     # → [0, 0, 0]
len(xs)                     # → 4
3 in xs                     # → True
list(range(3))              # → [0, 1, 2]
min(xs)                     # → 1
max(xs)                     # → 5
sum(xs)                     # → 13
```

---

## Slicing

`seq[start:stop:step]` — `start` inclusive, `stop` exclusive, every part optional. Works on any sequence.

| Task | Code |
|---|---|
| first n | `xs[:n]` |
| last n | `xs[-n:]` |
| all but the last n | `xs[:-n]` |
| a window | `xs[i:j]` |
| every second | `xs[::2]` |
| reversed copy | `xs[::-1]` |
| shallow copy | `xs[:]` |

```python
xs = [0, 1, 2, 3, 4, 5, 6]

xs[2:5]                     # → [2, 3, 4]
xs[:3]                      # → [0, 1, 2]
xs[4:]                      # → [4, 5, 6]
xs[-2:]                     # → [5, 6]
xs[:-2]                     # → [0, 1, 2, 3, 4]
xs[::2]                     # → [0, 2, 4, 6]
xs[1::2]                    # → [1, 3, 5]
xs[::-1]                    # → [6, 5, 4, 3, 2, 1, 0]
xs[::-2]                    # → [6, 4, 2, 0]

xs[:100]                    # → [0, 1, 2, 3, 4, 5, 6]    slices never raise
xs[100:]                    # → []
# xs[100]                                                IndexError -- indexes do

ys = [1, 2, 3, 4]
ys[1:3] = [9]                             # slice assignment
ys                          # → [1, 9, 4]
del ys[0:1]
ys                          # → [9, 4]
```

---

## Dictionaries

Insertion order is preserved (guaranteed from 3.7).

| Method | Returns |
|---|---|
| `d[k]` | value; raises `KeyError` |
| `d.get(k[, default])` | value, or `default` (`None` if omitted) |
| `d.setdefault(k[, default])` | `d[k]`, inserting `default` first if absent |
| `d.pop(k[, default])` | value, removing it; raises without a default |
| `d.popitem()` | removes and returns the last inserted pair |
| `d.keys()` `d.values()` `d.items()` | dynamic views |
| `d.update(other)` | merge in place |
| `d.copy()` | shallow copy, same as `dict(d)` |
| `dict.fromkeys(iterable[, value])` | new dict from keys |

```python
d = {"a": 1, "b": 2}

d["a"]                      # → 1
d.get("zz")                 # → None
d.get("zz", 0)              # → 0
"a" in d                    # → True
d["c"] = 3
len(d)                      # → 3

d.setdefault("a", 99)       # → 1         already present, unchanged
d.setdefault("e", 5)        # → 5         inserted
d.pop("e")                  # → 5
d.pop("nope", None)         # → None

list(d)                     # → ['a', 'b', 'c']      bare iteration gives KEYS
list(d.values())            # → [1, 2, 3]
list(d.items())             # → [('a', 1), ('b', 2), ('c', 3)]

d.update({"c": 30, "f": 6})
d["c"]                      # → 30

dict.fromkeys(["x", "y"], 0)        # → {'x': 0, 'y': 0}
{**{"a": 1}, **{"b": 2}}            # → {'a': 1, 'b': 2}
{"a": 1} | {"b": 2}                 # → {'a': 1, 'b': 2}      3.9+

orig = {"k": 1}
alias = orig                              # another name for the SAME object
copy = dict(orig)                         # a genuine copy
orig["new"] = 2
"new" in alias              # → True
"new" in copy               # → False

e = {"a": 1, "b": 2}
for k in list(e):                         # list() snapshots the keys first;
    if k == "a":                          # resizing while iterating raises
        del e[k]
e                           # → {'b': 2}
```

---

## Sets

Unordered, no duplicates, O(1) membership. `set()` for an empty one — `{}` is a dict.

| Operation | Method | Operator |
|---|---|---|
| union | `a.union(b)` | `a \| b` |
| intersection | `a.intersection(b)` | `a & b` |
| difference | `a.difference(b)` | `a - b` |
| symmetric difference | `a.symmetric_difference(b)` | `a ^ b` |
| subset / superset | `a.issubset(b)` | `a <= b` / `a >= b` |
| add / remove | `add(v)`, `discard(v)` safe, `remove(v)` raises | |

```python
a, b = {1, 2, 3}, {3, 4}

a | b                       # → {1, 2, 3, 4}
a & b                       # → {3}
a - b                       # → {1, 2}
a ^ b                       # → {1, 2, 4}
{1, 2} <= a                 # → True

s = set()
s.add(1)
s.add(1)
len(s)                      # → 1
s.discard(99)                             # no error if absent
# s.remove(99)                            KeyError

set([3, 1, 3])              # → {1, 3}
sorted({3, 1, 2})           # → [1, 2, 3]     sets have no order
frozenset([1, 2]) == frozenset([2, 1])      # → True    hashable, usable as a key
```

---

## Tuples

Immutable sequences. Compared element by element, left to right — which makes them useful as sort keys and dict keys.

```python
t = (1, "b", 3)

t[0]                        # → 1
len(t)                      # → 3
(1, 2) + (3,)               # → (1, 2, 3)
isinstance((1,), tuple)     # → True        the trailing comma makes the tuple
isinstance((1), int)        # → True        without it, just parentheses

x, y, z = t                               # unpacking
y                           # → 'b'
first, *rest = [1, 2, 3]
rest                        # → [2, 3]
*init, last = [1, 2, 3]
init                        # → [1, 2]

p, q = 1, 2
p, q = q, p                               # swap
(p, q)                      # → (2, 1)

(1, "b") < (1, "c")         # → True        element-wise comparison
(2, "a") > (1, "z")         # → True
{(1, 2): "ok"}[(1, 2)]      # → 'ok'        tuples hash, lists do not
```

---

## Comprehensions

| Form | Produces |
|---|---|
| `[f(x) for x in it]` | list |
| `[f(x) for x in it if p(x)]` | filtered list |
| `[a if p else b for x in it]` | ternary in the value position |
| `{k: v for ...}` | dict |
| `{x for ...}` | set |
| `(x for ...)` | generator, lazy |
| `[y for row in rows for y in row]` | nested, outer loop first |

```python
xs = [1, 2, 3, 4, 5]

[x * 2 for x in xs]                     # → [2, 4, 6, 8, 10]
[x for x in xs if x % 2 == 0]           # → [2, 4]
[x * 2 for x in xs if x > 3]            # → [8, 10]
[x if x > 2 else 0 for x in xs]         # → [0, 0, 3, 4, 5]

grid = [[1, 2], [3, 4]]
[v for row in grid for v in row]        # → [1, 2, 3, 4]
[[v * 2 for v in row] for row in grid]  # → [[2, 4], [6, 8]]

{x: x ** 2 for x in [1, 2]}             # → {1: 1, 2: 4}
{x % 3 for x in xs}                     # → {0, 1, 2}
sum(x * 2 for x in xs)                  # → 30     generator, no brackets needed

[y for x in xs if (y := x * 3) > 6]     # → [9, 12, 15]     walrus, 3.8+
```

---

## Control flow

```python
x = 5

if x > 10:
    label = "big"
elif x > 3:
    label = "mid"
else:
    label = "small"
label                       # → 'mid'

"even" if x % 2 == 0 else "odd"     # → 'odd'        ternary

# for / else -- the else runs only if the loop did NOT break
found = None
for n in [1, 2, 3]:
    if n == 2:
        found = n
        break
else:
    found = "none"
found                       # → 2

acc = []
for n in range(10):
    if n % 2:
        continue                          # skip
    if n > 6:
        break                             # stop
    acc.append(n)
acc                         # → [0, 2, 4, 6]

i, w = 0, []
while i < 3:
    w.append(i)
    i += 1
w                           # → [0, 1, 2]

# match, 3.10+
def kind(v):
    match v:
        case 0:
            return "zero"
        case int():
            return "int"
        case [a, b]:
            return f"pair {a}{b}"
        case _:
            return "other"

kind(0)                     # → 'zero'
kind(7)                     # → 'int'
kind([1, 2])                # → 'pair 12'
kind("s")                   # → 'other'
```

---

## Functions

| Syntax | Meaning |
|---|---|
| `def f(a, b=1)` | positional with a default |
| `def f(*args)` | collects extra positionals into a tuple |
| `def f(**kwargs)` | collects extra keywords into a dict |
| `def f(a, /, b)` | `a` is positional-only (3.8+) |
| `def f(a, *, b)` | `b` is keyword-only |
| `lambda x: expr` | single-expression function |
| `f(*seq)` / `f(**dict)` | unpack at the call site |
| `yield` | makes it a generator |

```python
def basic(a, b=10, *args, key=None, **kwargs):
    return a, b, args, key, kwargs

basic(1)                            # → (1, 10, (), None, {})
basic(1, 2, 3, key="k", z=9)        # → (1, 2, (3,), 'k', {'z': 9})

def keyword_only(a, *, b):
    return a + b
keyword_only(1, b=2)                # → 3

double = lambda v: v * 2
double(4)                           # → 8

def stats(xs):
    return min(xs), max(xs)                   # multiple returns are just a tuple
stats([3, 1, 4])                    # → (1, 4)

def adder(n):                                 # closure
    def inner(v):
        return v + n
    return inner
adder(3)(4)                         # → 7

def add3(a, b, c):
    return a + b + c
add3(*[1, 2, 3])                    # → 6
add3(**{"a": 1, "b": 2, "c": 3})    # → 6

def counter(n):                               # generator
    for i in range(n):
        yield i
list(counter(3))                    # → [0, 1, 2]

def outer():
    total = 0
    def bump():
        nonlocal total
        total += 1
    bump()
    bump()
    return total
outer()                             # → 2

# default arguments are evaluated ONCE, at definition time
def bad(item, into=[]):
    into.append(item)
    return into
bad(1)                              # → [1]
bad(2)                              # → [1, 2]      the same list, reused

def good(item, into=None):
    into = [] if into is None else into
    into.append(item)
    return into
good(1)                             # → [1]
good(2)                             # → [2]
```

---

## Classes

| Syntax | Meaning |
|---|---|
| `def __init__(self, ...)` | constructor |
| `self` | the instance; explicit first parameter, never passed at the call site |
| `self.x = x` | this line *is* the field declaration |
| attribute in the class body | shared by every instance |
| `@property` | accessed without parentheses |
| `@staticmethod` | no `self` |
| `@classmethod` | receives the class as `cls` |
| `super().__init__(...)` | call the parent |

```python
class Point:
    origin = (0, 0)                           # class attribute, shared

    def __init__(self, x, y=0):
        self.x = x
        self.y = y

    def move(self, dx):
        self.x += dx
        return self

    @property
    def magnitude(self):
        return (self.x ** 2 + self.y ** 2) ** 0.5

    @staticmethod
    def describe():
        return "a point"

    @classmethod
    def at_origin(cls):
        return cls(0, 0)

    def __repr__(self):
        return f"Point({self.x}, {self.y})"

    def __eq__(self, other):
        return (self.x, self.y) == (other.x, other.y)

    def __lt__(self, other):
        return self.magnitude < other.magnitude

    def __len__(self):
        return 2


p = Point(3, 4)
p.x                                 # → 3
p.magnitude                         # → 5.0     no parentheses: it is a property
Point.describe()                    # → 'a point'
Point.at_origin().x                 # → 0
repr(Point(1, 2))                   # → 'Point(1, 2)'
Point(1, 2) == Point(1, 2)          # → True
Point(1, 1) < Point(5, 5)           # → True
len(Point(1, 2))                    # → 2
Point.origin                        # → (0, 0)


class Point3D(Point):
    def __init__(self, x, y, z):
        super().__init__(x, y)
        self.z = z

q = Point3D(1, 2, 3)
q.z                                 # → 3
isinstance(q, Point)                # → True
q.move(1).x                         # → 2       inherited


from dataclasses import dataclass, field, replace

@dataclass
class Item:
    name: str                                 # dataclass fields REQUIRE annotations
    qty: int = 0
    tags: list = field(default_factory=list)

i = Item("bolt", 3)
i.tags                              # → []
replace(i, qty=9).qty               # → 9
Item("a", 1) == Item("a", 1)        # → True    __eq__ generated for you
```

Common dunders: `__init__ __repr__ __str__ __eq__ __lt__ __hash__ __len__ __getitem__ __setitem__ __contains__ __iter__ __next__ __call__ __enter__ __exit__`.

---

## Exceptions

| Clause | When it runs |
|---|---|
| `try:` | always |
| `except E as e:` | if `E` or a subclass was raised |
| `except (A, B):` | either type |
| `else:` | only if nothing was raised |
| `finally:` | always, raised or not |
| `raise` | re-raise the current exception |
| `raise X from Y` | chain the cause |

```python
def risky(n):
    if n < 0:
        raise ValueError("negative: " + str(n))
    return n

try:
    risky(-1)
except ValueError as e:
    caught = str(e)
except (TypeError, KeyError):
    caught = "other"
else:
    caught = "no error"
finally:
    cleaned = True

caught                              # → 'negative: -1'
cleaned                             # → True


class MyError(Exception):
    pass

try:
    raise MyError("boom")
except MyError as e:
    msg = str(e)
msg                                 # → 'boom'

issubclass(KeyError, LookupError)   # → True
issubclass(IndexError, LookupError) # → True
issubclass(ValueError, Exception)   # → True
```

Hierarchy worth knowing: `BaseException` → `Exception` → { `ArithmeticError` → `ZeroDivisionError` · `LookupError` → `IndexError`, `KeyError` · `OSError` · `RuntimeError` → `RecursionError` · `TypeError` · `ValueError` · `AttributeError` · `NameError` · `StopIteration` }.

---

## Sorting

`sorted()` returns a new list; `list.sort()` sorts in place and returns `None`. Both are **stable** — equal keys keep their existing relative order.

| Task | Code |
|---|---|
| ascending | `sorted(xs)` |
| descending | `sorted(xs, reverse=True)` |
| by a computed key | `sorted(xs, key=lambda v: v.n)` |
| descending numerically | `key=lambda v: -v.n` |
| descending, ties ascending | `key=lambda v: (-v.n, v.name)` |
| descending on a string field | sort twice, least significant key first |
| by dict value | `sorted(d, key=lambda k: -d[k])` |
| top n | `sorted(...)[:n]` |
| reverse without sorting | `reversed(xs)` |

```python
items = [("banana", 5), ("apple", 5), ("cherry", 9), ("date", 1)]

sorted([3, 1, 2])                             # → [1, 2, 3]
sorted([3, 1, 2], reverse=True)               # → [3, 2, 1]
sorted(items, key=lambda p: p[1])[0]          # → ('date', 1)
sorted(items, key=lambda p: -p[1])[0]         # → ('cherry', 9)

# a tuple key sorts by the first element, then the second as a tie-break
sorted(items, key=lambda p: (-p[1], p[0]))[1]        # → ('apple', 5)
sorted(items, key=lambda p: (-p[1], p[0]))[2]        # → ('banana', 5)

# reverse=True flips EVERY key in a tuple, which is usually not wanted
sorted(items, key=lambda p: (p[1], p[0]), reverse=True)[1]   # → ('banana', 5)

# to reverse a non-numeric field, sort twice, least significant first
rows = [("a", 2), ("b", 1), ("c", 2)]
rows.sort(key=lambda r: r[1])
rows.sort(key=lambda r: r[0], reverse=True)
rows                                          # → [('c', 2), ('b', 1), ('a', 2)]

from operator import itemgetter
sorted(items, key=itemgetter(1))[0]           # → ('date', 1)

d = {"bob": 3, "ann": 9}
sorted(d)                                     # → ['ann', 'bob']
sorted(d.items(), key=lambda kv: -kv[1])[0]   # → ('ann', 9)
sorted(d, key=lambda k: -d[k])                # → ['ann', 'bob']
list(reversed([1, 2, 3]))                     # → [3, 2, 1]
```

---

## Iteration helpers

```python
xs = ["a", "b", "c"]

list(enumerate(xs))                 # → [(0, 'a'), (1, 'b'), (2, 'c')]
list(enumerate(xs, start=1))[0]     # → (1, 'a')
list(zip([1, 2, 3], "xy"))          # → [(1, 'x'), (2, 'y')]    stops at shortest
list(reversed(xs))                  # → ['c', 'b', 'a']

list(range(3))                      # → [0, 1, 2]
list(range(2, 6))                   # → [2, 3, 4, 5]
list(range(0, 10, 3))               # → [0, 3, 6, 9]
list(range(3, 0, -1))               # → [3, 2, 1]

any(x == "b" for x in xs)           # → True
all(len(x) == 1 for x in xs)        # → True
any([])                             # → False
all([])                             # → True      vacuously true

sum(1 for x in xs if x > "a")       # → 2         count matching
max(xs, key=len)                    # → 'a'       first of the ties
max([], default=None)               # → None

pairs = [(1, "a"), (2, "b")]
list(zip(*pairs))                   # → [(1, 2), ('a', 'b')]    unzip
```

---

## Built-in functions

| Function | Purpose |
|---|---|
| `len(x)` | length |
| `sum(iterable[, start])` | total |
| `min` / `max(iterable, *, key, default)` | extremes |
| `sorted(iterable, *, key, reverse)` | new sorted list |
| `reversed(seq)` | reverse iterator |
| `enumerate(iterable, start=0)` | index/value pairs |
| `zip(*iterables)` | parallel iteration |
| `any` / `all(iterable)` | boolean reduce |
| `abs` `round` `divmod` `pow` | numeric |
| `int` `float` `str` `bool` `list` `dict` `set` `tuple` | conversion |
| `isinstance(o, t)` `issubclass(c, t)` `type(o)` | type checks |
| `range(stop)` / `(start, stop[, step])` | integer sequence |
| `map(f, it)` / `filter(f, it)` | lazy transform |
| `ord` / `chr` | character ↔ code point |
| `bin` `hex` `oct` | base representation |
| `next(it[, default])` | pull one item |
| `getattr` `setattr` `hasattr` | attribute access |

```python
list(map(str, [1, 2]))                      # → ['1', '2']
list(filter(lambda v: v > 1, [1, 2, 3]))    # → [2, 3]
next(iter([5, 6]))                          # → 5
next((v for v in [1, 2] if v > 5), None)    # → None
isinstance(1, int)                          # → True
type([]) is list                            # → True
hasattr("s", "upper")                       # → True
getattr("s", "upper")()                     # → 'S'
```

---

## Standard library

### bisect — binary search on a sorted sequence

| Call | Returns |
|---|---|
| `bisect_right(a, x)` | insertion point **after** any equal entries |
| `bisect_left(a, x)` | insertion point **before** them |
| `bisect_right(a, x) - 1` | index of the last entry `<= x` |
| `bisect_left(a, x)` | index of the first entry `>= x` |
| `insort(a, x)` | insert, keeping order |

```python
from bisect import bisect_left, bisect_right, insort

a = [0, 5, 10, 20]
bisect_right(a, 10)                 # → 3
bisect_left(a, 10)                  # → 2
bisect_right(a, 12) - 1             # → 2
a[bisect_right(a, 12) - 1]          # → 10
bisect_left(a, 7)                   # → 2
insort(a, 7)
a                                   # → [0, 5, 7, 10, 20]
```

The `key=` parameter needs 3.10+. On older versions keep a parallel list of keys and search that.

### collections

```python
from collections import defaultdict, Counter, deque, namedtuple

dd = defaultdict(list)
dd["a"].append(1)                             # no KeyError; the default is built
dict(dd)                            # → {'a': [1]}

di = defaultdict(int)
di["x"] += 1
di["x"]                             # → 1

c = Counter("abracadabra")
c["a"]                              # → 5
c.most_common(2)[0]                 # → ('a', 5)
sum(c.values())                     # → 11

q = deque([2, 3])
q.appendleft(1)
q.append(4)
list(q)                             # → [1, 2, 3, 4]
q.popleft()                         # → 1       O(1) at both ends
q.pop()                             # → 4

P = namedtuple("P", "x y")
pt = P(1, 2)
pt.x                                # → 1
tuple(pt)                           # → (1, 2)
```

### itertools

```python
from itertools import (count, cycle, islice, chain, combinations,
                       permutations, product, groupby, accumulate, pairwise)

list(islice(count(10), 3))          # → [10, 11, 12]
list(islice(cycle("ab"), 5))        # → ['a', 'b', 'a', 'b', 'a']
list(chain([1, 2], [3]))            # → [1, 2, 3]
list(combinations([1, 2, 3], 2))    # → [(1, 2), (1, 3), (2, 3)]
list(permutations([1, 2], 2))       # → [(1, 2), (2, 1)]
list(product([1, 2], "ab"))[0]      # → (1, 'a')
list(accumulate([1, 2, 3]))         # → [1, 3, 6]       running totals
list(pairwise([1, 2, 3]))           # → [(1, 2), (2, 3)]    3.10+

rows = [("a", 1), ("a", 2), ("b", 3)]
{k: [v for _, v in g] for k, g in groupby(rows, key=lambda r: r[0])}
                                    # → {'a': [1, 2], 'b': [3]}
```

`groupby` only groups *adjacent* equal keys, so sort the input first.

### math, heapq, functools

```python
import math, heapq
from functools import reduce, lru_cache

math.floor(2.7)                     # → 2
math.ceil(2.1)                      # → 3
math.sqrt(9)                        # → 3.0
math.gcd(12, 18)                    # → 6
math.isclose(0.1 + 0.2, 0.3)        # → True
math.factorial(5)                   # → 120

h = [5, 1, 3]
heapq.heapify(h)
h[0]                                # → 1       min-heap
heapq.heappush(h, 0)
heapq.heappop(h)                    # → 0
heapq.nlargest(2, [1, 9, 4])        # → [9, 4]
heapq.nsmallest(2, [1, 9, 4])       # → [1, 4]

reduce(lambda a, b: a * b, [1, 2, 3, 4])    # → 24

@lru_cache(maxsize=None)
def fib(n):
    return n if n < 2 else fib(n - 1) + fib(n - 2)
fib(30)                             # → 832040
```

### json, re, copy, datetime

```python
import json, re, copy
from datetime import datetime, timedelta, date

json.loads('{"a": [1, 2]}')         # → {'a': [1, 2]}
json.dumps({"a": 1})                # → '{"a": 1}'

re.search(r"\d+", "abc123").group()             # → '123'
re.findall(r"\d", "a1b2")                       # → ['1', '2']
re.sub(r"\s+", " ", "a   b")                    # → 'a b'
re.split(r"[,;]", "a,b;c")                      # → ['a', 'b', 'c']
re.match(r"(\w+)-(\d+)", "item-42").group(1)    # → 'item'

nested = {"a": [1, 2]}
shallow = copy.copy(nested)
deep = copy.deepcopy(nested)
nested["a"].append(3)
shallow["a"]                        # → [1, 2, 3]     shares the inner list
deep["a"]                           # → [1, 2]        fully independent

d1 = date(2024, 3, 1)
(d1 + timedelta(days=10)).day       # → 11
(date(2024, 3, 11) - d1).days       # → 10
datetime(2024, 1, 2, 3, 4).strftime("%Y-%m-%d %H:%M")   # → '2024-01-02 03:04'
datetime.strptime("2024-01-02", "%Y-%m-%d").year        # → 2024
```

---

## Type conversion

```python
int("42")                           # → 42
int(3.9)                            # → 3       truncates toward zero
int(True)                           # → 1
float("3.5")                        # → 3.5
str(42)                             # → '42'
bool(0)                             # → False
bool("0")                           # → True
list("abc")                         # → ['a', 'b', 'c']
list({"a": 1})                      # → ['a']       keys
list({"a": 1}.items())              # → [('a', 1)]
tuple([1, 2])                       # → (1, 2)
set([1, 1, 2])                      # → {1, 2}
dict([("a", 1)])                    # → {'a': 1}
dict(zip("ab", [1, 2]))             # → {'a': 1, 'b': 2}
"".join(["a", "b"])                 # → 'ab'
"".join(str(n) for n in [1, 2])     # → '12'
# int("abc")                                    ValueError
```

---

## General idioms

Compositions that come up constantly and are not written down as such in the language reference.

| Task | Code |
|---|---|
| even / odd | `n % 2 == 0` |
| midpoint index | `len(xs) // 2` |
| middle element | `xs[len(xs) // 2]` |
| round up | `-(-a // b)` |
| clamp | `max(lo, min(hi, x))` |
| mean | `sum(xs) / len(xs)` |
| sign | `(x > 0) - (x < 0)` |
| is it empty | `if not xs:` |
| safe first | `xs[0] if xs else None` |
| counter increment | `d[k] = d.get(k, 0) + 1` |
| append into a list value | `d.setdefault(k, []).append(v)` |
| key of the largest value | `max(d, key=lambda k: d[k])` |
| invert a dict | `{v: k for k, v in d.items()}` |
| count matching | `sum(1 for x in xs if p(x))` |
| first match, or None | `next((x for x in xs if p(x)), None)` |
| index of the largest | `max(range(len(xs)), key=lambda i: xs[i])` |
| safe index | `xs.index(v) if v in xs else -1` |
| dedupe, keep order | `list(dict.fromkeys(xs))` |
| has duplicates | `len(xs) != len(set(xs))` |
| flatten one level | `[y for row in rows for y in row]` |
| chunk by n | `[xs[i:i+n] for i in range(0, len(xs), n)]` |
| consecutive pairs | `zip(xs, xs[1:])` |
| rotate left by k | `xs[k:] + xs[:k]` |
| group into lists | `d.setdefault(k, []).append(v)` |
| basename | `path.split("/")[-1]` |
| reverse a string | `s[::-1]` |
| sort characters | `"".join(sorted(s))` |
| letter index | `ord(c) - ord("a")` |
| in a range | `lo <= x < hi` |
| intervals overlap | `a0 < b1 and b0 < a1` |

```python
xs = [10, 20, 30, 40, 50]
d = {}

7 % 2 == 0                          # → False     even?
-(-17 // 5)                         # → 4         round up
max(0, min(10, 15))                 # → 10        clamp
sum(xs) / len(xs)                   # → 30.0      mean
(5 > 0) - (5 < 0)                   # → 1         sign

len(xs) // 2                        # → 2         midpoint index
xs[len(xs) // 2]                    # → 30        middle element
xs[-1]                              # → 50
xs[-2:]                             # → [40, 50]
not []                              # → True      is it empty
xs[0] if xs else None               # → 10        safe first

d["k"] = d.get("k", 0) + 1
d["k"]                              # → 1
d.setdefault("g", []).append(1)
d["g"]                              # → [1]

scores = {"a": 3, "b": 9}
max(scores, key=lambda k: scores[k])            # → 'b'
{v: k for k, v in scores.items()}               # → {3: 'a', 9: 'b'}

sum(1 for x in xs if x > 25)                    # → 3
next((x for x in xs if x > 25), None)           # → 30
max(range(len(xs)), key=lambda i: xs[i])        # → 4
xs.index(30) if 30 in xs else -1                # → 2

dup = [3, 1, 3, 2]
list(dict.fromkeys(dup))                        # → [3, 1, 2]
len(dup) != len(set(dup))                       # → True
[v for row in [[1], [2, 3]] for v in row]       # → [1, 2, 3]
[xs[i:i + 2] for i in range(0, len(xs), 2)][0]  # → [10, 20]
list(zip(xs, xs[1:]))[0]                        # → (10, 20)
xs[2:] + xs[:2]                                 # → [30, 40, 50, 10, 20]

rows = [("a", 1), ("b", 2), ("a", 3)]
groups = {}
for k, v in rows:
    groups.setdefault(k, []).append(v)
groups                                          # → {'a': [1, 3], 'b': [2]}

"/dir/sub/f.txt".split("/")[-1]                 # → 'f.txt'
"abc"[::-1]                                     # → 'cba'
"".join(sorted("cba"))                          # → 'abc'
ord("c") - ord("a")                             # → 2

0 <= 7 < 10                                     # → True

def overlaps(a0, a1, b0, b1):
    return a0 < b1 and b0 < a1
overlaps(0, 10, 5, 15)                          # → True
overlaps(0, 10, 10, 20)                         # → False   touching is not overlapping

events = [(2, "x"), (11, "y"), (20, "z")]
[e for t, e in events if 20 - 10 < t <= 20]     # → ['y', 'z']    the last 10 units
```

---

## Gotchas

| Trap | Reality |
|---|---|
| `xs = xs.sort()` | `.sort()` returns `None`; you just destroyed the list |
| `b = a` | never copies; use `dict(a)` / `list(a)` / `a[:]` |
| `dict(a)` on nested data | shallow; inner objects are shared |
| `def f(x, acc=[])` | the default is created once and reused across calls |
| `if size:` | `0` is falsy, so this rejects a legitimate zero |
| `x == None` | use `x is None` |
| `-7 // 2` | `-4`, not `-3`; floors toward −∞ |
| `0.1 + 0.2 == 0.3` | `False`; compare with a tolerance |
| `isinstance(True, int)` | `True`; bool is a subclass of int |
| `all([])` | `True`; `any([])` is `False` |
| `(1)` | an int; a one-tuple needs `(1,)` |
| modifying a list while iterating | skips elements; build a new list |
| `lambda: i` inside a loop | late binding; use `lambda i=i: i` |

```python
xs = [3, 1]
xs.sort()                           # → None

a = {"k": 1}
b = a                                         # another name, not a copy
a["n"] = 2
"n" in b                            # → True

outer = {"inner": [1]}
sh = dict(outer)                              # shallow
outer["inner"].append(2)
sh["inner"]                         # → [1, 2]

not 0                               # → True
not ""                              # → True
bool("0")                           # → True
bool([0])                           # → True

-7 // 2                             # → -4
int(-7 / 2)                         # → -3
0.1 + 0.2 == 0.3                    # → False

isinstance(True, int)               # → True
True + True                         # → 2
[1, True].count(1)                  # → 2

all([])                             # → True
any([])                             # → False
1 < 2 < 3                           # → True

fs = [lambda: i for i in range(3)]
fs[0]()                             # → 2     every closure sees the final i
gs = [lambda i=i: i for i in range(3)]
gs[0]()                             # → 0     bound at definition time
```

---

## Version notes

| Feature | Requires |
|---|---|
| f-strings | 3.6 |
| dict insertion order guaranteed | 3.7 |
| `:=` walrus operator | 3.8 |
| positional-only `/` in signatures | 3.8 |
| `dict \| dict` merge | 3.9 |
| `str.removeprefix` / `removesuffix` | 3.9 |
| `match` statement | 3.10 |
| `bisect(..., key=...)` | 3.10 |
| `itertools.pairwise` | 3.10 |

Check with `import sys; print(sys.version)`.