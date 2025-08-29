# 🧵 Django Silk

## 📌 What is Django Silk?
- Django profiling tool for:
  - SQL queries
  - Request/response time
  - Middleware performance
  - Duplicate queries

## ⚙️ Installation
```bash
pip install django-silk

```

## 🛠️ Setup in settings.py

```python
INSTALLED_APPS += ['silk']
MIDDLEWARE = ['silk.middleware.SilkyMiddleware', ...]
```

## URL

```python
# urls.py
path('silk/', include('silk.urls', namespace='silk'))


```

## ✅ Use Cases

- Detect N+1 queries
    
- View request/response time
    
- Profile individual views/functions
    
- Optimize SQL queries

