# Understanding the order of `super()` calls in Django/DRF overrides

When overriding methods in Django or Django REST Framework (DRF), the position of the `super()` call relative to your custom code depends on what you want to achieve:

---

## Two common patterns:

### 1. Call `super()` **first**, then modify the result

Used when the parent method returns a value that you want to customize or refine.

```python
def get_queryset(self):
    qs = super().get_queryset()  # Get base queryset
    return qs.filter(user=self.request.user)  # Filter/customize it
```

- You first get the "base" output from the parent.
    
- Then apply your custom logic on top of it.
    
- Typical for methods that return data you want to adjust

### 2. Set or prepare something **before** calling `super()`

Used when you need to configure state or attributes that the parent method relies on.


``` python
def get_permissions(self):    
	self.permission_classes = [AllowAny]     
		if self.request.method == 'POST':         
			self.permission_classes = [IsAdminUser]     
		return super().get_permissions()  # Parent uses updated permission_classes`

```

- You update or prepare class attributes or state first.
    
- Then call `super()` to execute the base logic which uses that updated state.
    
- Typical for methods that build something based on the current object's attributes.

## Why does the order matter?

- The parent method may **depend on current attributes or state** (so set those first).
    
- Or it may **return something you want to modify** (so get that first, then modify).
    
- Understanding the contract of the method you're overriding is key.
## Summary table:

|Pattern|When to use|
|---|---|
|`result = super().method(); modify(result)`|When the method returns data you want to customize.|
|`prepare_state(); result = super().method()`|When the method uses internal state/attributes to operate.|
### Example in Django/DRF:

- `get_queryset()` usually returns a queryset you want to filter/customize → call `super()` first.
    
- `get_permissions()` relies on `self.permission_classes` attribute → set attribute first, then call `super()`.
    

---

> **Tip:** When overriding, always check if you need to prepare context or state before calling `super()`, or if you need to modify the result after.


Related notes:
[[Django notes]]

#python #django #drf