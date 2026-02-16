# Django ORM Basics

This repository documents my hands-on learning and exploration of Django’s Object Relational Mapper (ORM). It covers the fundamentals of defining models, running migrations, and performing database operations using Django’s built-in ORM.

This is part of my self-learning journey to deeply understand backend development with Django.

---

## Objectives

The goal of this project is to understand how Django interacts with databases using Python instead of raw SQL.

Key learning areas include:

- Understanding Django ORM architecture
- Creating and managing models
- Running migrations
- Performing CRUD operations
- Querying the database using Django ORM
- Understanding relationships between models
- Using Django shell for database interaction

---

## Topics Covered

### 1. Models

- Creating Django models
- Model fields (CharField, IntegerField, DateField, DateTimeField, EmailField, etc.)
- Field options (null, blank, default, unique)

Example:

```python
from django.db import models

class Artist(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField(unique=True)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.name
````

---

### 2. Migrations

Commands learned:

```bash
python manage.py makemigrations
python manage.py migrate
```

Purpose:

* Convert models into database tables
* Keep database schema in sync with code
* Track database changes safely over time

---

### 3. CRUD Operations

Create:

```python
Artist.objects.create(name="Trish", email="trish@example.com")
```

Read:

```python
Artist.objects.all()
Artist.objects.get(id=1)
```

Update:

```python
artist = Artist.objects.get(id=1)
artist.name = "Patricia"
artist.save()
```

Delete:

```python
artist.delete()
```

---

### 4. Querying

Filtering:

```python
Artist.objects.filter(name="Trish")
```

Getting a single object:

```python
Artist.objects.get(id=1)
```

Ordering:

```python
Artist.objects.order_by("-created_at")
```

Checking existence:

```python
Artist.objects.filter(email="test@email.com").exists()
```

---

### 5. Django Shell

Used for interacting directly with the database:

```bash
python manage.py shell
```

Example:

```python
from myapp.models import Artist

Artist.objects.create(
    name="Test Artist",
    email="test@email.com"
)

Artist.objects.all()
```

---

## Project Structure

```
django-orm-basics/
│
├── myproject/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── asgi.py
│
├── myapp/
│   ├── __init__.py
│   ├── models.py
│   ├── views.py
│   ├── admin.py
│   └── migrations/
│
├── manage.py
└── README.md
```

---

## Key Concepts Learned

* How Django abstracts SQL using Python
* How models represent database tables
* How migrations manage database schema changes
* How to perform database operations safely using ORM
* How Django improves development speed and security
* How QuerySets work internally

---

## Why Django ORM Matters

Django ORM allows developers to:

* Write database logic using Python instead of SQL
* Improve development speed
* Build scalable backend systems
* Reduce human error in database queries
* Maintain clean and readable backend code

---

## Status

Completed foundational Django ORM concepts.

Planned next topics:

* ForeignKey relationships
* One-to-Many relationships
* Many-to-Many relationships
* Advanced QuerySets
* Aggregations and annotations
* Query optimization

---

## How to Run This Project

Clone the repository:

```bash
git clone https://github.com/yourusername/django-orm-basics.git
```

Navigate into the folder:

```bash
cd django-orm-basics
```

Run migrations:

```bash
python manage.py makemigrations
python manage.py migrate
```

Start Django shell:

```bash
python manage.py shell
```

---

## Author

**Patricia Njuguna**
Software Engineer | Backend & Full Stack Developer

Focused on mastering backend engineering through structured, production-level learning.
