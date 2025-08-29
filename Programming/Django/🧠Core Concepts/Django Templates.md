# Django Templates: Adding and Registering 

## 📌 What Are Templates in Django?

Templates in Django are **HTML files** used to define the structure and layout of web pages. Django’s template system allows you to insert dynamic content (like data from a database) into HTML using template tags and variables.

---

## 🧱 Folder Structure – Where to Put Templates?

You can place templates in two main ways:

### ✅ Option 1: App-Level Templates (Recommended)

Inside each app, create a folder named `templates`, and then another folder named the same as your app:

arduino

CopyEdit

`project/ ├── myapp/ │   ├── templates/ │   │   └── myapp/ │   │       └── home.html`

### ✅ Option 2: Global Templates Folder

You can also create a global `templates` folder at the same level as your `manage.py`:

csharp

CopyEdit

`project/ ├── templates/ │   └── base.html`

You can use **both**, but app-level templates are cleaner and scale better.

---

## ⚙️ Registering Templates in `settings.py`

Django needs to know where to find templates. This is handled in the `TEMPLATES` setting inside `settings.py`.

python

CopyEdit

```python
import os  TEMPLATES = [     {         'BACKEND': 'django.template.backends.django.DjangoTemplates',         'DIRS': [os.path.join(BASE_DIR, 'templates')],  # Global folder (optional)         'APP_DIRS': True,  # Important: Enables searching inside each app's templates/ folder         'OPTIONS': {             'context_processors': [                 'django.template.context_processors.debug',                 'django.template.context_processors.request',                 ...             ],         },     }, ]
```

### Explanation:

- `DIRS`: A list of directories where Django will look for templates (for global templates).
    
- `APP_DIRS`: If `True`, Django will also look inside each app's `templates/` folder.
    
- `BASE_DIR`: This points to the root of your Django project.
