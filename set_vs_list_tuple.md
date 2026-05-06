# Set vs List vs Tuple in Python

## Question

What is the difference between set, list and tuple?

---

## Answer

All three hold collections of items, but they differ in **ordering, duplicates, mutability, and what they're built for**.

### The three at a glance

```python
lst = [1, 2, 3, 2, 1]      # list  — ordered, duplicates allowed, mutable
tup = (1, 2, 3, 2, 1)      # tuple — ordered, duplicates allowed, immutable
st  = {1, 2, 3, 2, 1}      # set   — unordered, NO duplicates, mutable

print(lst)   # [1, 2, 3, 2, 1]
print(tup)   # (1, 2, 3, 2, 1)
print(st)    # {1, 2, 3}    — duplicates silently removed
```

### Comparison table

| | List | Tuple | Set |
|---|---|---|---|
| Syntax | `[1, 2, 3]` | `(1, 2, 3)` | `{1, 2, 3}` |
| Mutable? | Yes | No | Yes |
| Ordered (preserves insertion order)? | Yes | Yes | No* |
| Indexable (`x[0]`)? | Yes | Yes | No |
| Slicing (`x[1:3]`)? | Yes | Yes | No |
| Duplicates allowed? | Yes | Yes | No |
| Element type restriction | Any | Any | Must be hashable (no lists/dicts/sets inside) |
| Hashable itself? | No | Yes (if elements are) | No (use `frozenset`) |
| Can be a dict key? | No | Yes | No |
| Membership test (`x in seq`) speed | O(n) — slow | O(n) — slow | O(1) — fast |

\* Sets have *no* concept of order. Iteration order in CPython is an implementation detail, not guaranteed.

### Empty containers — watch the syntax

```python
empty_list  = []
empty_tuple = ()
empty_set   = set()      # NOT {} — that's an empty dict!
empty_dict  = {}
```

`{}` is an empty dict, not an empty set. The set literal syntax `{...}` only works when there's at least one element.

### What sets are actually for

Sets are not just "lists without duplicates" — they're built for **fast membership testing** and **mathematical set operations**.

**1. O(1) lookup vs O(n) for list/tuple:**

```python
import time

big_list = list(range(1_000_000))
big_set  = set(range(1_000_000))

# list — has to scan
t0 = time.time(); 999_999 in big_list; print(time.time()-t0)   # ~0.01s

# set — direct hash lookup
t0 = time.time(); 999_999 in big_set;  print(time.time()-t0)   # ~0.000001s
```

If you're repeatedly asking "is X in this collection?" — convert to a set first.

**2. Set algebra:**

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

a | b    # union          → {1, 2, 3, 4, 5, 6}
a & b    # intersection   → {3, 4}
a - b    # difference     → {1, 2}
a ^ b    # symmetric diff → {1, 2, 5, 6}
```

**3. Deduplication in one line:**

```python
chainages = [0, 25, 50, 25, 75, 50, 100]
unique = list(set(chainages))     # [0, 25, 50, 75, 100] — but order not guaranteed!

# If order matters:
unique_ordered = list(dict.fromkeys(chainages))   # [0, 25, 50, 75, 100]
```

### What sets cannot do

```python
s = {10, 20, 30}

s[0]              # TypeError — sets aren't indexable
s[0] = 99         # TypeError — no positional assignment
s.append(40)      # AttributeError — use s.add(40) instead
s[0:2]            # TypeError — no slicing
```

No order means no indexing, no slicing, no "first element."

### What can go inside each

```python
# Lists and tuples can hold anything
[1, "a", [2, 3], {4, 5}]              # fine
(1, "a", [2, 3], {4, 5})              # fine

# Sets can only hold hashable (immutable) elements
{1, "a", (2, 3)}                      # fine — tuple is hashable
{1, "a", [2, 3]}                      # TypeError: unhashable type: 'list'
{1, "a", {2, 3}}                      # TypeError: unhashable type: 'set'
```

This is why a "set of sets" requires `frozenset` (the immutable cousin of `set`):

```python
{frozenset({1, 2}), frozenset({3, 4})}    # works
```

### The intent difference

- **List** → an ordered, growable sequence of similar items. *"All the survey points in order."*
- **Tuple** → a fixed record where each position has meaning. *"One survey point: (chainage, offset, level)."*
- **Set** → a bag of unique items where you only care about membership, not order. *"Which chainages have we already processed?"*

A real workflow can use all three together:

```python
# survey points — a list of tuples
points = [(0.0, -3.5, 42.18),
          (25.0, -3.5, 41.95),
          (50.0, -3.5, 41.72)]

# unique chainages seen — a set
seen_chainages = {p[0] for p in points}     # {0.0, 25.0, 50.0}

# check membership fast
if 25.0 in seen_chainages:
    print("already processed")
```

### Quick mental model

- **List** → a notebook page: ordered, editable, may have repeats
- **Tuple** → a printed receipt: ordered, fixed, may have repeats
- **Set** → a bag of marbles: no order, no duplicates, you can only ask "is this marble in here?"

### When to pick which

| You want to… | Use |
|---|---|
| Keep items in order, modify often | list |
| Return multiple values from a function | tuple |
| Use as a dict key or store in another set | tuple (or frozenset) |
| Check "is X in this collection?" repeatedly | set |
| Remove duplicates | set |
| Combine collections (union, intersection) | set |
| Store coordinates / fixed records | tuple |
| Build up data dynamically (`append`, `sort`) | list |
