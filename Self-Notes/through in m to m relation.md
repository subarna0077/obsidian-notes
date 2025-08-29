# 📚 Understanding `through` in Many-to-Many Relationships

## ✅ Default Many-to-Many
- Django creates an automatic join table.
- You cannot add extra fields.

## 🧩 Custom `through`
- Use when you need extra data on the relationship.
- Define an intermediate model manually.

## 🧠 Database Perspective
- Without `through` = simple join table with foreign keys.
- With `through` = fully customizable table with extra fields.

## 🔥 Example

```python
class Enrollment(models.Model):
    student = models.ForeignKey(Student, on_delete=models.CASCADE)
    course = models.ForeignKey(Course, on_delete=models.CASCADE)
    enrolled_on = models.DateField()
    grade = models.CharField(max_length=2)

class Course(models.Model):
    name = models.CharField(max_length=100)
    students = models.ManyToManyField(Student, through='Enrollment')

```

## 💡 When to use it?

- When you want to **store data about the relationship itself**, like timestamps, status, grades, etc.

#through #database #joins 