
## 🔐 `get_permissions()` in Django REST Framework (DRF)

### 📌 What is it?

`get_permissions()` is a method in Django REST Framework views that allows **dynamic control over permissions** based on the incoming request.

---

### 🧰 Default Behavior

```python
`permission_classes = [AllowAny]  # Default permission  

 def get_permissions(self):     
	 return [permission() for permission in self.permission_classes]`
```


- If not overridden, all requests are allowed (`AllowAny`) unless changed.
- `permission_classes` is a **class attribute**, usually set statically.
---
### ✨ When & Why to Override?

Override `get_permissions()` to apply **different permission logic** based on:

- Request method (`GET`, `POST`, etc.)
    
- Request user
    
- View-specific conditions
    

---

### ✅ Example Use Case

```python
`class ProductListCreateAPIView(generics.ListCreateAPIView):   
	queryset = Product.objects.all()     
	serializer_class = ProductSerializer 
	     
	def get_permissions(self):         
		self.permission_classes = [AllowAny]  # Default for GET         
		if self.request.method == 'POST':             
				self.permission_classes = [IsAdminUser]  # POST only for admins 
		return super().get_permissions()
```


- **GET** → Anyone can view products
    
- **POST** → Only admins can create products
    

---

### 🧠 Summary

|Aspect|Behavior|
|---|---|
|Default|`AllowAny` (no restriction)|
|Custom `get_permissions()`|Enables dynamic permissions per request|
|Common Use Cases|Different rules for read vs write actions|

---

## 📎 Related Concepts

- `permission_classes`
    
- `IsAdminUser`, `IsAuthenticated`, `AllowAny`
    
- Class-based views (`ListCreateAPIView`)
    
- Role-based access control
    
