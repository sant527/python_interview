# Python Closures, Cells, and Decorators — Quick Summary

---

## 1. Closure

### What is a closure?

A **closure** is a function that **remembers variables from its enclosing (outer) scope**, even after that outer function has finished executing.

> **Closure = function + remembered variables**

---

### Example

```python
def outer():
    x = 10
    def inner():
        return x
    return inner

f = outer()
print(f())  # 10
```

Even though `outer()` has finished, `inner()` still knows `x`.

### Why does this work?

Because Python keeps the referenced variable alive as long as the inner function exists.

---

## 2. Cell

### What is a cell?

A **cell** is an internal container object used by Python to store variables captured by a closure.

You don't create cells manually — Python creates them automatically.

### Mental model

```
Cell
 └── value → x = 10
```

If:

- a variable is defined in an outer function, **and**
- used in an inner function

👉 Python stores it in a **cell**

---

### Inspecting a cell

```python
print(f.__closure__)
print(f.__closure__[0].cell_contents)
```

- `__closure__` → tuple of cells
- each cell holds one captured variable

---

## 3. Why cells exist

Cells allow **multiple inner functions to share the same variable**.

```python
def outer():
    x = 0

    def inc():
        nonlocal x
        x += 1
        return x

    def dec():
        nonlocal x
        x -= 1
        return x

    return inc, dec
```

Here:

```
inc ─┐
     ├── shared cell → x
dec ─┘
```

---

## 4. Decorators (using closures)

### Basic decorator

```python
def my_decorator(func):
    def wrapper():
        print("Before")
        func()
        print("After")
    return wrapper
```

### Usage

```python
say_hello = my_decorator(say_hello)
say_hello()
```

### What's really happening?

- `func` is captured by `wrapper`
- `func` is stored inside a **cell**
- `wrapper` is a **closure**
- Even after `my_decorator` finishes, `func` still exists

### Key point

The function is **not executed** during decoration — only the **reference** is stored.

---

## 5. Execution timeline

### Decoration time

```python
say_hello = my_decorator(say_hello)
```

- `wrapper` is created
- `func` is captured into a cell
- `my_decorator` exits

### Call time

```python
say_hello()
```

- `wrapper()` runs
- `func()` (original function) executes here

---

## 6. LEGB rule (where closures fit)

| Scope | Meaning              |
| ----- | -------------------- |
| L     | Local                |
| E     | Enclosing (closure)  |
| G     | Global               |
| B     | Built-in             |

Closures live in the **Enclosing (E)** scope.

---

## 7. Common misconceptions

- ❌ Closure stores function result
  ✅ Closure stores function **reference**

- ❌ Variables die when outer function exits
  ✅ Variables live as long as the closure exists

- ❌ Cells are Python variables
  ✅ Cells are **internal storage objects**

---

## 8. One-line takeaway

- **Decorator** = syntax using closures
- **Closure** = function + cells
- **Cell** = box holding captured variables
