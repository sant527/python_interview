# Variable, Instance, Object in Python

## Question

Source: https://www.geeksforgeeks.org/python/mutable-vs-immutable-objects-in-python/

> **Mutable vs Immutable Objects in Python**
> Last Updated : 11 Jul, 2025
>
> In Python, Every variable in Python holds an instance of an object. There are two types of objects in Python i.e. Mutable and Immutable objects. Whenever an object is instantiated, it is assigned a unique object id. The type of the object is defined at the runtime and it can't be changed afterward. However, its state can be changed if it is a mutable object.

I see three terms — **variable, instance, object** — explain more details on this.

---

## Answer

Good catch — those three terms get used loosely in that paragraph. Here's the precise distinction:

**Object** is the actual thing in memory. Every value in Python — `5`, `"hello"`, `[1,2,3]`, even functions and classes — is an object. Each object has three properties:
- **identity** (a unique ID, returned by `id()`)
- **type** (`int`, `list`, `str`, etc., returned by `type()`)
- **value** (the data it holds)

**Instance** is just an object viewed *through the lens of its class*. When we say "`5` is an instance of `int`," we mean the object `5` was created from the `int` class. Every object is an instance of some class — so "instance" and "object" often refer to the same thing, but "instance" emphasizes the class relationship.

**Variable** is a name that refers to an object. Unlike C or VBA, Python variables don't *contain* values — they're labels pointing to objects in memory. The object lives in memory; the variable is just a tag stuck on it.

Here's a concrete example:

```python
x = [1, 2, 3]
```

- `[1, 2, 3]` — the **object** (a list living in memory)
- That object is an **instance** of the `list` class
- `x` — the **variable**, a name pointing to that object

You can verify each piece:

```python
print(id(x))          # identity of the object
print(type(x))        # <class 'list'> — confirms it's an instance of list
print(isinstance(x, list))  # True
```

Two variables can point to the *same* object:

```python
x = [1, 2, 3]
y = x          # y is a new variable, but points to the same list object
y.append(4)
print(x)       # [1, 2, 3, 4] — both names see the change
print(id(x) == id(y))  # True
```

This is exactly why mutability matters. If you do `y.append(4)`, you're mutating the *object* — and every variable pointing to it sees the change. But if you do `y = y + [4]`, Python creates a *new* list object and rebinds `y` to it; `x` still points to the original.

Contrast with an immutable object:

```python
a = 10
b = a
b += 1         # creates a new int object 11 and rebinds b
print(a, b)    # 10 11 — a is untouched
```

You can't mutate `10` itself — `int` objects have no methods that change their value — so `b += 1` has to make a new object.

So in the GfG sentence "every variable holds an instance of an object," the wording is a bit sloppy. More precisely: **every variable is a name bound to an object, and that object is an instance of some class.**
