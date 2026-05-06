# Short-Circuit Evaluation in Python

**Short-circuit evaluation** in Python is a behavior of logical operators like `and` and `or` where the second operand is **only evaluated if necessary**.

---

## How it works:

### 1. `and` operator

* Expression: `A and B`
* If `A` is **False**, Python **does not evaluate `B`** because the whole expression will already be False.
* If `A` is **True**, then Python evaluates `B`.

#### Example:

```python
x = 0
y = 10

result = x != 0 and (y / x > 1)
```

* `x != 0` is `False`
* Python **stops there**, so `(y / x)` is never executed → avoids division by zero

---

### 2. `or` operator

* Expression: `A or B`
* If `A` is **True**, Python **does not evaluate `B`** because the result is already True.
* If `A` is **False**, then Python evaluates `B`.

#### Example:

```python
x = 5
y = 10

result = x > 0 or (y / 0 > 1)
```

* `x > 0` is `True`
* Python **skips** `(y / 0)` → no error

---

## Why it’s useful

* Prevents errors (like division by zero)
* Improves performance (avoids unnecessary computation)
* Enables concise conditional logic

---

## Important note

Python doesn’t just return `True` or `False`:

* `and` returns the **first falsy value** or the **last value**
* `or` returns the **first truthy value** or the **last value**

#### Example:

```python
print(0 and 5)   # 0
print(5 and 10)  # 10
print(0 or 5)    # 5
print(5 or 10)   # 5
```

---

## Summary

Short-circuit evaluation helps Python run efficiently by skipping unnecessary checks, preventing errors, and making logical expressions cleaner.
