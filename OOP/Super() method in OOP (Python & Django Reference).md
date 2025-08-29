
# 🔁 Understanding `super()` in Python (OOP Concept)

## 📌 What is `super()`?

In Object-Oriented Programming, `super()` is a built-in Python function used to **refer to the parent class**. It's commonly used to call methods defined in a **superclass (parent class)** from a **subclass (child class)**.

---

## 🔍 Syntax

```python 
super().method_name(arguments)
```

Used typically in:

Constructors (__init__)

Overridden methods

Frameworks like Django (views, serializers, models, etc.)

🧠 Why Use super()?
✅ Reuse parent class behavior without rewriting

✅ Maintain clean and DRY (Don't Repeat Yourself) code

✅ Enhance or extend inherited behavior

📦 Real-World Analogy
Imagine you inherit a machine from your parent. Instead of building your own from scratch, you start it (super()), then add custom parts to modify its behavior.

## 🧱 Example 1: Basic Python

```python
class Animal:
    def sound(self):
        print("Some animal sound")
        
class Dog(Animal):
    def sound(self):
        super().sound()  # Call parent method
        print("Bark!")

```
🔄 Output:
Some animal sound
Bark!
🔗 super().sound() runs the parent's method before adding its own.

## 🧰 Example 2: Constructor in Python

```
class Person:
    def __init__(self, name):
        self.name = name

class Student(Person):
    def __init__(self, name, grade):
        super().__init__(name)  # Call parent constructor
        self.grade = grade
```

## 🌐 Example 3: super() in Django
```python
from rest_framework import generics

class UserOrderListAPIView(generics.ListAPIView):
    queryset = Order.objects.prefetch_related('items').all()
    serializer_class = OrderSerializer

    def get_queryset(self):
        qs = super().get_queryset()  # Inherits the default queryset
        return qs.filter(user=self.request.user)  # Applies custom logic

```

## 🧠 How It Works:
super().get_queryset() calls the parent method from ListAPIView
It returns the default queryset

You customize it by filtering based on the logged-in user

## 🧩 When Should You Use super()?

- When overriding a method in a subclass but still want to retain the base functionality.
- In frameworks like Django, Flask, or DRF where parent classes offer a lot of reusable logic
- In multi-level or multiple inheritance to delegate method resolution order (MRO) cleanly


## ⚠️ Don't Confuse:

- super() calls methods from the parent class
- self refers to the current instance
- Without super(), you may break framework behavior

## ✅ Summary

Concept	   Explanation
super()	       Access parent class methods
Used In	   Constructors, overridden methods, Django views/models/forms
Benefits	   Clean code, reusability, extensibility
Key Point	   Keeps the base behavior intact while adding new logic

🔗 Related Notes
[[inheritance_in_python]]

[[method_overriding]]

[[init_method_in_classes]]