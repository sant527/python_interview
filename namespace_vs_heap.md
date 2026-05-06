# 🧠 Python Names, Objects, and Memory Model

## 1️⃣ Where are names stored?

- Names (variables like `a`, `b`) are stored in **namespaces**
- A namespace is essentially a **dictionary**
- Namespaces map **name → object reference**

### Common namespaces

- **Local** – inside functions
- **Global** – module-level variables
- **Built-in** – `len`, `print`, etc.

### Example

```python
a = 2
```

Conceptually:

```python
globals() == {
    'a': <object 2>
}
```

➡️ The name `a` points to the object `2`

---

## 2️⃣ Where are objects stored?

- Objects live in **heap memory**
- Objects are **independent of variable names**
- Multiple names can point to the same object

### Each object stores

- Type information (`int`, `list`, etc.)
- Reference count (in CPython)
- Actual value/data

### Conceptual structure

```
[ PyObject ]
 ├── reference count
 ├── type pointer (int)
 └── value (2)
```

---

## 3️⃣ What is `id()`?

- In CPython, `id(obj)` is the **memory address** of the object
- It uniquely identifies an object during its lifetime

### Example

```python
a = 2
print(id(a))  # memory address of integer object 2
```

> ⚠️ Other Python implementations may not expose raw memory addresses, but `id()` is still unique.

---

## 4️⃣ Names vs Objects (key concept)

```python
a = 2
b = a
```

### Memory model

```
Namespace
 ├── 'a' ───▶ object(2)
 └── 'b' ───▶ object(2)

Heap
 └── object(2)
     ├── type: int
     ├── refcount: 2
     └── value: 2
```

✔ One object  ✔ Two names  ✔ Reference count = 2

---

## 5️⃣ What happens on reassignment?

```python
a = 3
```

Now:

```
Namespace
 ├── 'a' ───▶ object(3)
 └── 'b' ───▶ object(2)

Heap
 ├── object(2)  (refcount = 1)
 └── object(3)
```

- The original object `2` is unchanged
- Only the **name binding** changed

---

## 6️⃣ Immutability and mutability

### Immutable objects (`int`, `str`, `tuple`)

- Cannot be modified in place
- Reassignment creates a new object

### Mutable objects (`list`, `dict`)

- Object's internal data can change
- Names remain bound to the same object

---

## 📌 Key Takeaway (Interview-ready)

> In Python, **variables are names** stored in namespaces (dictionaries).
> **Objects live in heap memory** and contain type, reference count, and value.
> Names **reference** objects; they are not part of the object itself.

---

## 🧩 Analogy

- **Object** → House
- **Name** → Address label
- **Namespace** → Address book

> Changing the label doesn't change the house.
