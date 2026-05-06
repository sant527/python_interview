# `is` vs `==` in Python

`is` and `==` are **not the same** in Python, and confusing them can lead to subtle bugs.

---

## `==` (Equality)

* Checks whether **values are equal**
* Uses the object’s `__eq__()` method

### Example:

```python
a = [1, 2, 3]
b = [1, 2, 3]

print(a == b)  # True (same contents)
```

---

## `is` (Identity)

* Checks whether two variables refer to the **same object in memory**

### Example:

```python
a = [1, 2, 3]
b = [1, 2, 3]

print(a is b)  # False (different objects)
```

---

## When they *look* the same

Sometimes `is` appears to behave like `==`:

```python
x = 10
y = 10

print(x is y)  # True (often)
```

This happens because Python **reuses small integers and some strings** (optimization called *interning*), but you should **not rely on this**.

---

## Correct usage

* Use `==` → for comparing values
* Use `is` → for comparing identity (same object)

---

## Important special case

Use `is` for `None`:

```python
x = None

if x is None:   # correct
    print("x is None")
```

---

## Quick summary

| Operator | Meaning         | Use for           |
| -------- | --------------- | ----------------- |
| `==`     | Value equality  | Data comparison   |
| `is`     | Object identity | Same object check |

---

## Summary

* `==` compares **values**
* `is` compares **memory identity**
* Always use `is` when checking against `None`
* Do not rely on `is` for value comparison, even if it sometimes appears to work
