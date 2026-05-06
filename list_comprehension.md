# Python List Comprehension — Summary

---

## 1. What is a List Comprehension?

A **list comprehension** is a concise and readable way to create a list from an iterable.

> **List comprehension = compact `for` loop that builds a list**

---

## 2. Basic Syntax

```python
[expression for item in iterable]
```

**Example**

```python
squares = [x * x for x in range(5)]
```

**Equivalent loop:**

```python
squares = []
for x in range(5):
    squares.append(x * x)
```

---

## 3. List Comprehension with `if` (filtering)

```python
evens = [x for x in range(10) if x % 2 == 0]
```

**Meaning:** Take `x` from `range(10)` only if the condition is true.

---

## 4. `if-else` in List Comprehension

✔ **Correct syntax:**

```python
labels = ["even" if x % 2 == 0 else "odd" for x in range(5)]
```

❌ **Incorrect syntax:**

```python
[x for x in range(5) if x % 2 == 0 else "odd"]
```

---

## 5. Nested List Comprehension

```python
matrix = [[i * j for j in range(3)] for i in range(3)]
```

**Equivalent loop:**

```python
matrix = []
for i in range(3):
    row = []
    for j in range(3):
        row.append(i * j)
    matrix.append(row)
```

---

## 6. Using Functions

```python
def square(x):
    return x * x

result = [square(x) for x in range(5)]
```

---

## 7. Flattening a List

```python
matrix = [[1, 2], [3, 4], [5, 6]]
flat = [x for row in matrix for x in row]
```

**Execution order:**

```python
for row in matrix:
    for x in row:
```

---

## 8. List Comprehension vs `map()` / `filter()`

```python
# List comprehension
squares = [x*x for x in range(5) if x % 2 == 0]

# map + filter
squares = list(map(lambda x: x*x, filter(lambda x: x % 2 == 0, range(5))))
```

✔ List comprehensions are usually more readable.

---

## 9. Performance Notes

- Faster than normal `for` loops
- Uses more memory than generator expressions

```python
gen = (x * x for x in range(10**6))  # generator expression
```

---

## 10. Common Mistakes

❌ **Overly complex one-liners**

```python
[x+y for x in a for y in b if x > 0 if y > 0]
```

❌ **Using comprehensions for side effects**

```python
[print(x) for x in range(5)]  # bad practice
```

---

## 11. One-Line Takeaway

> **List comprehension is the Pythonic way to create lists using loops and conditions.**
