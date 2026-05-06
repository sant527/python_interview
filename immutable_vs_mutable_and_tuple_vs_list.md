# Mutable vs Immutable in Python

## Question

Explain what is mutable vs immutable.

---

## Answer

The distinction is simple: **can the object's value be changed *in place* after it's created?**

- **Mutable** → yes, you can modify the object itself
- **Immutable** → no, once created, it's frozen; any "change" actually creates a new object

### Which is which in Python

| Immutable | Mutable |
|---|---|
| `int`, `float`, `complex` | `list` |
| `str` | `dict` |
| `tuple` | `set` |
| `bool` | `bytearray` |
| `frozenset` | user-defined classes (by default) |
| `bytes` | |

### Mutable example — the object changes

```python
x = [1, 2, 3]
print(id(x))   # e.g. 140234567890
x.append(4)
print(x)       # [1, 2, 3, 4]
print(id(x))   # 140234567890 — SAME id, same object, just modified
```

The list `x` points to is the *same object* before and after `append`. Its internal state changed.

### Immutable example — a new object is created

```python
s = "hello"
print(id(s))   # e.g. 140234111111
s = s + " world"
print(s)       # "hello world"
print(id(s))   # 140234222222 — DIFFERENT id, brand new object
```

The original `"hello"` was never modified — it can't be. Python built a new string `"hello world"` and rebound `s` to it. The old `"hello"` gets garbage-collected if nothing else references it.

Try to mutate a string directly and Python refuses:

```python
s = "hello"
s[0] = "H"     # TypeError: 'str' object does not support item assignment
```

Same with tuples:

```python
t = (1, 2, 3)
t[0] = 99      # TypeError: 'tuple' object does not support item assignment
```

### Why this matters in practice

**1. Aliasing bugs with mutables.** Two names pointing to the same mutable object means changes through one name are visible through the other:

```python
a = [1, 2, 3]
b = a            # b is NOT a copy — same object
b.append(99)
print(a)         # [1, 2, 3, 99] — surprise!
```

With immutables this never happens, because you can't mutate the shared object — you can only rebind your own name to a new one.

**2. Default argument trap.** A classic Python gotcha:

```python
def add_item(item, bucket=[]):   # mutable default — shared across calls!
    bucket.append(item)
    return bucket

print(add_item(1))   # [1]
print(add_item(2))   # [1, 2] — not [2]!
```

The default `[]` is created *once* when the function is defined, and every call without an explicit `bucket` reuses that same list. Fix with `bucket=None` and create a fresh list inside the function.

**3. Dict keys and set members must be immutable.** Python uses an object's hash to put it in a dict/set. If the object could mutate, its hash could change and the dict would lose track of it. So:

```python
d = {(1, 2): "ok"}      # tuple key — fine
d = {[1, 2]: "fail"}    # TypeError: unhashable type: 'list'
```

**4. Tuples can *contain* mutables — and that's a sharp edge.** A tuple itself is immutable (you can't swap out its elements), but if one of its elements is a list, that list is still mutable:

```python
t = (1, 2, [3, 4])
t[2].append(5)
print(t)         # (1, 2, [3, 4, 5]) — tuple "changed"
t[2] = [9]       # TypeError — can't rebind the slot itself
```

The tuple's *structure* is frozen; the *objects it points to* may not be.

### Quick mental model

A variable is a sticky note with a name on it, stuck to an object. With **mutable** objects, you can edit the object while the note stays put. With **immutable** objects, the object is read-only — to "change" it, you peel the note off and stick it on a different object.

---

# Tuple vs List

## Question

tuple vs list

---

## Answer

Both are ordered sequences that can hold any mix of types — but they differ in **mutability, syntax, and intent**.

### The core difference

```python
lst = [1, 2, 3]      # list — mutable
tup = (1, 2, 3)      # tuple — immutable

lst[0] = 99          # works → [99, 2, 3]
tup[0] = 99          # TypeError: 'tuple' object does not support item assignment

lst.append(4)        # works
tup.append(4)        # AttributeError: 'tuple' object has no attribute 'append'
```

A list can grow, shrink, and have elements swapped. A tuple is frozen the moment it's created.

### Syntax

```python
lst = [1, 2, 3]            # square brackets
tup = (1, 2, 3)            # parentheses (optional)
tup = 1, 2, 3              # also a tuple — commas make it, not parens

single_list  = [42]        # one-element list
single_tuple = (42,)       # one-element tuple — comma is REQUIRED
not_a_tuple  = (42)        # just the int 42 in parens
empty_list   = []
empty_tuple  = ()
```

That trailing comma for single-element tuples trips everyone up at least once.

### Comparison table

| | List | Tuple |
|---|---|---|
| Mutable? | Yes | No |
| Syntax | `[1, 2, 3]` | `(1, 2, 3)` or `1, 2, 3` |
| Methods | ~11 (`append`, `pop`, `sort`, `reverse`, …) | 2 (`count`, `index`) |
| Memory | Larger (over-allocates for growth) | Smaller, tightly packed |
| Construction speed | Slower | Faster |
| Hashable? | No (can't be dict key / set member) | Yes, *if* all elements are hashable |
| Can be a dict key? | No | Yes |
| Typical use | Homogeneous, variable-length collection | Fixed record / heterogeneous fields |

### Memory and speed — concrete numbers

```python
import sys
sys.getsizeof([1, 2, 3, 4, 5])    # ~104 bytes
sys.getsizeof((1, 2, 3, 4, 5))    # ~80 bytes
```

Lists allocate extra slots so `append` is fast on average — that's the memory overhead. Tuples allocate exactly what's needed.

```python
import timeit
timeit.timeit("[1,2,3,4,5]", number=10_000_000)   # slower
timeit.timeit("(1,2,3,4,5)", number=10_000_000)   # faster
```

For literals in tight loops, tuples win. CPython even caches small constant tuples.

### The intent difference (this matters more than the speed)

The Python community follows a strong convention:

- **List** = a sequence of *similar* things, length unknown or variable
  → "all the cross-section chainages from this survey" → `[0.0, 25.0, 50.0, 75.0, ...]`
- **Tuple** = a fixed-length *record* where each position has a meaning
  → "one survey point: chainage, offset, level" → `(125.0, -3.5, 42.18)`

Rule of thumb: if you'd want to `for x in seq:` and treat each element the same → list. If each position means something different → tuple.

### Things only tuples can do

**1. Be a dictionary key or set member:**

```python
levels = {}
levels[(125.0, -3.5)] = 42.18         # (chainage, offset) → level — works
levels[[125.0, -3.5]] = 42.18         # TypeError: unhashable type: 'list'
```

This alone is reason enough that tuples exist. Coordinates, composite keys, anything you want to look up by — tuple.

**2. Multiple return values and unpacking:**

```python
def stats(values):
    return min(values), max(values), sum(values)/len(values)

lo, hi, avg = stats([1, 2, 3, 4, 5])    # tuple unpacking
```

Every time you write `a, b = b, a` to swap, you're creating and unpacking a tuple.

### Things only lists can do

```python
data = [1, 2, 3]
data.append(4)              # add to end
data.insert(0, 0)           # insert at index
data.remove(2)              # remove first matching value
data.pop()                  # remove and return last
data.sort()                 # sort in place
data.reverse()              # reverse in place
data[1:3] = [20, 30, 40]    # slice assignment
```

None of these have tuple equivalents — for a tuple you'd build a new tuple from scratch.

### A subtle gotcha — tuples can still hold mutables

A tuple's *structure* is immutable, but it doesn't deep-freeze its contents:

```python
t = (1, 2, [3, 4])
t[2].append(5)
print(t)             # (1, 2, [3, 4, 5]) — tuple "changed"
t[2] = [9]           # TypeError — can't rebind the slot

hash(t)              # TypeError: unhashable type: 'list'
                     # because it contains a mutable element
```

A tuple is hashable only if *every* element is hashable. A tuple of lists is not a valid dict key.

### When to use which — quick guide

Use a **list** when:
- You'll add, remove, or reorder elements
- All elements are the "same kind of thing"
- You need sorting, slicing assignment, or in-place mutation

Use a **tuple** when:
- The collection is a fixed record (`(x, y, z)`, `(name, age, role)`)
- You need it as a dict key or in a set
- You're returning multiple values from a function
- You want a clear signal "this won't change"
- You want slightly less memory and faster construction

### Quick mental model

A **list** is a notebook page — you can scribble, cross out, add lines.
A **tuple** is a printed receipt — the values are set, you can read them, but you can't edit them.
