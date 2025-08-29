
### ## 📘 What is a Relationship in a Database?

In a **relational database**, data is stored in **tables**, and relationships define **how the data in one table relates to data in another**.

This helps avoid data duplication and makes querying much easier and powerful.

## 🧱 Common Relationship Types

|Relationship Type|Example|Django Equivalent|
|---|---|---|
|One-to-One|One user has one profile|`OneToOneField`|
|One-to-Many (Many-to-One)|One author writes many books|`ForeignKey`|
|Many-to-Many|A student can take many courses, and a course has many students|`ManyToManyField`|

### 1. 🔗 One-to-One (1:1)

**Meaning**: One record in table A is linked to exactly one record in table B.

📌 **Example**: Each `User` has **one** `Profile`.

#### In SQL:
```sql
Profile has user_id referencing User
CREATE TABLE User (
    id INT PRIMARY KEY,
    username VARCHAR(100)
);

CREATE TABLE Profile (
    id INT PRIMARY KEY,
    user_id INT UNIQUE,
    FOREIGN KEY (user_id) REFERENCES User(id)
);

```

In Django:
```python
class User(models.Model):
    username = models.CharField(max_length=100)

class Profile(models.Model):
    user = models.OneToOneField(User,
    on_delete=models.CASCADE)
    bio = models.TextField()


```

### 2. 🔁 One-to-Many (Many-to-One)

**Meaning**: One record in table A can be linked to **many** records in table B.

📌 **Example**: One `Author` writes many `Books`.

#### In SQL:



```sql
CREATE TABLE Author (id INT PRIMARY KEY,name VARCHAR(100) );  
CREATE TABLE Book (id INT PRIMARY KEY,title VARCHAR(100),author_id INT,FOREIGN KEY (author_id) REFERENCES Author(id) );
```
`

#### In Django:

```python
class Author(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)

```

You can access:

- `book.author` to get the author of a book
    
- `author.book_set.all()` to get all books by the author (or use `related_name`)


### 3. 🔁 Many-to-Many

**Meaning**: Records in both tables can be linked to many in the other.

📌 **Example**: A `Student` can take multiple `Courses`, and a `Course` has many `Students`.

In SQL:

This is done using a **junction table** (also called a through table):

```sql
CREATE TABLE Student (
    id INT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE Course (
    id INT PRIMARY KEY,
    title VARCHAR(100)
);

CREATE TABLE Student_Course (
    student_id INT,
    course_id INT,
    PRIMARY KEY(student_id, course_id),
    FOREIGN KEY (student_id) REFERENCES Student(id),
    FOREIGN KEY (course_id) REFERENCES Course(id)
);

```

In Django:
```python
class Student(models.Model):
	name = models.CharField(100)

class Course(models.Model):
	title = models.CharField(100)
	student = models.ManytoManyField('Student', through='Enrollment')

class Enrollment(models.Model):
	student = models.ForeignKey(Student, 
		on_delete=models.CASCADE)
	course = models.ForeignKey(Course, on_delete= 
		models.CASCADE)
	
```


🧠 When to Use Which?

| Situation                              | Relationship |
| -------------------------------------- | ------------ |
| Each item has exactly one related item | One-to-One   |
| One item relates to multiple others    | One-to-Many  |
| Many items relate to many others       | Many-to-Many |



 #Django #SQL #Database
