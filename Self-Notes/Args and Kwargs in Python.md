## ✅ What are `*args` and `**kwargs`?

These are used to **pass a variable number of arguments** to a function or method.

---
## 🔹 `*args` → Positional arguments

- `*args` lets you pass any number of **positional arguments** (those without keyword names).
- Inside the function, `args` will be a tuple.

🔸 Example:
```python
def example_func(*args):
    print(args)

example_func(1, 2, 3)  
# Output: (1, 2, 3)

```

## 🔹 `**kwargs` → Keyword arguments

- `**kwargs` lets you pass any number of **keyword arguments** (`key=value`).
    
- Inside the function, `kwargs` will be a dictionary.

🔸 Example:

```python
def example_func(**kwargs):
    print(kwargs)

example_func(name='Subarna', age=22)
# Output: {'name': 'Subarna', 'age': 22}
```

---

## ✅ Why are `*args` and `**kwargs` useful?

They help when:

1. You don't know how many arguments will be passed.
    
2. You want to **forward arguments** to a parent method (like in Django views or DRF mixins).

## In DRF / Django CBVs

You’ll often see:

```python
def get(self, request, *args, **kwargs):
    ...

```

Here’s what each means:

| Parameter  | Meaning                                                 |
| ---------- | ------------------------------------------------------- |
| `self`     | refers to the instance of the class (standard in OOP)   |
| `request`  | the HTTP request object                                 |
| `*args`    | extra positional arguments (like URL path variables)    |
| `**kwargs` | extra keyword arguments (like `pk=3` from URL patterns) |

🔍 Real Example in Django

Suppose your URL is:
```bash
/products/5/

```

And your URL config is:
```python
path('products/<int:pk>/', ProductDetailView.as_view(), name='product-detail')
```

Then inside your view:

```python
def get(self, request, *args, **kwargs):
    print(kwargs)  # {'pk': 5}

```

## ✅ Forwarding Args in Mixins

When DRF mixins like `ListModelMixin` use:

def list(self, request, *args, **kwargs):
    ...

They're saying:

> “I might receive extra arguments from Django or the router. I’ll accept them just in case — and pass them forward to any method that needs them.”

## 🧠 Summary: Easy Table

|Term|Type|Inside function becomes|Example|
|---|---|---|---|
|`*args`|Positional|Tuple|`(1, 2, 3)`|
|`**kwargs`|Keyword|Dict|`{'name': 'Subarna'}`|

## Bonus: How to Forward Them

You can also **pass them forward** like this:
```python
def my_view(request, *args, **kwargs):
    return another_view(request, *args, **kwargs)

```

#python #drf #django 
